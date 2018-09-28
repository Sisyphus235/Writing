# 1.常用pytest命令
```shell
pytest -v -s -l xxx/xxx
# -v是verbose看到详细信息
# -s是打印出运行代码部分的print
# -l是列出local变量

pytest -tb=short --cache-clear xxx/xxx/xxx.py::<function>
# --tb=short表示traceback精简显示，如果是no则不显示
# --cache-clear表示不使用缓存运行
# xxx.py::<function>是指定某个文件中的某个函数来执行
```
> mock的object不能用hasattr(object, \<attribute>)来检验是否有某属性，所有的返回都是True
```python
from unittest.mock import Mock
a = Mock()
hasattr(a, 'b')

>>> True
```

# 2.常用测试代码

```python
import pytest
from unittest.mock import Mock  # 用于mock class或者function
from somewhere.somefile import somefunction  # 被mock的function/class

@pytest.fixture()  # 将mocked_fuction写入环境变量中
def mocked_function(mocker):  # mocker是pytest-mock中的功能
    mock_object = Mock()  # mock一个object
    mock_object.attr = {}  # 为mock的object添加attributes
    mocker.patch('mocked_function_path', return_value=mock_object)
    # 注意一定要写被测试单元所在路径，而不是somefunction所在路径
    # 将mocked_object作为返回值给mocked_function
    
@pytest.mark.usefixtures(mocked_function)  # 使用mocked_function中的mock_objeck
def test_some_case(mocker):  # 测例要以test开头
    assert somefunction(params) == expected_value 
    # somefunction中会使用mocked_function
```

有时不需要mock整个object，而是mock其中的一些method，例如

```python
class ForTest:
    field = 'origin'
    
    def method():
        pass
    
def test_for_test(mocker):
    test = ForTest()
    mock_method = mocker.patch.object(test, 'method')
    test.method()
    assert mock_method.called
    
    assert 'origin' == test.field
    mocker.patch.object(test, 'field', 'mocked')
    assert 'mocked' == test.field
```

# 3.Case Study

## Mocking tricky aspects
- What you mock
It's tricky when have a line of code that chains many structures together

```python
sth = AClass.a_class_method()["a_key"].a_method().an_attribute
```

- What the thing you mock normally returns
The more complicated the structure of the thing that is returned, the more complex the mocking will be.

## Mocking example-a database query
Take flask sqlalchemy as an example, using animal table

```python
class Animal(db.Model):
    __tablename__ = "animal"
    id = db.Column(db.Integer, primary_key=True)
    type = db.Column(db.String)
    noise = db.Column(db.String)
    
def get_animal_noises():
    animal_list = Animal.query.all()
    noises = [animal.noise for animal in animal_list]
    return noises
```

If there is one row on the animal table that contains:
> id: 1
 type: honey badger
 noise: RAAAHH

```python
@mock.patch("animals.Animal")
def test_get_noise(self, mock_animal):
    
    # Create test animal
    test_animal = Animal()
    test_animal.noise = "RAAAHH"
    test_animal.type = "honey badger"
    
    # Set return_value of all to list conatining test animal
    mock_animal.query.all.return_value = [test_animal]
    
    noises = get_animal_noises()
    assert(noises[0] == 'RAAAHH')
```

## Mocking example-something rediculous

Imagine a senario:
> Dragons spawn badgers. When badgers dance, a flower grows. When flowers grow, stars fall. When stars fall, hell breaks loose.

```python
from contextlib imoport contextmanager


class Dragon(object):
    @classmethod
    def spawn_badger(cls):
        new_badger = Badger()
        return new_badger
        
        
class Badger(object):
    def dance(self):
        print("The badger is dancing!")
        return [Flower()]
        
        
class Flower(object):
    def __init__(self):
        print("The flower is growing")
        self.star = {"the_star": Star()}
        
        
class Star(object):
    def fall(self):
        print("The stars are falling!")
        yield Hell()
        
        
class Hell(object):
    def break_loose(self):
        return True
        
    @contextmanager
    def initiate_breaking_loose(self):
        print("AHHH! Hell may break loose!")
        yield self.break_loose()
        print("Did hell break loose?")
```

```python
def break_hell_loose():
    hells = [hell for hell in Dragon.spawn_badger().dance()[0].star["the_star"].fall()]
    for hell in hells:
        with hell.initiate_breaking_loose() as broke_loose:
            if broke_loose:
                return "HELL BROKE LOOSE"
            else:
                return "HELL DID NOT BREAK LOOSE"
```

It’s pretty complicated to be mocked, which is solved below:

```python
@mock.patch('silly.Dragon')
def test_if_hell_broke_loose(self, mock_dragon):
    dance_return_mock = mock_dragon.spawn_badger.return_value.dance.return_value
    flower_mock = dance_return_mock.__getitem__.return_value
    star_dict_mock = flower_mock.star
    star_mock = star_dict_mock.__getitem__.return_value
    hell_mock = mock.MagicMock()
    hell_mock.initiate_breaking_loose.return_value.__enter__.return_value = False
    star_mock.fall.return_value = [hell_mock]
    broke_loose = break_hell_loose()
    assert(broke_loose == "HELL DID NOT BREAK LOOSE")
```

# 4.pytest fixture

pytest fixture是传统xUnit风格setup/teardown的替代品，它在以下几个方面有改进：
* fixture有明确的名称，并能声明使用scope，包括module, session, function等
* fixture的设计是模块化的，fixture名称会触发其函数，本身也可以被其他fixture调用，但注意调用的fixture的scope不能超过被调用的scope
* fixture管理从简单的单元扩展到复杂的函数测试，允许根据配置和组件选项参数化fixture和测试，或者在函数、类、模块或整个测试会话范围内重复使用fixture。

fixture的标记方法是@pytest.fixture，可以存储变量或者函数，--fixtures可以查看所有储存的内容

```python
import pytest

@pytest.fixture
def smtp():
    import smtplib
    return smtplib.SMTP('smtp.qq.com', 587, timeout=5)
    
def test_ehlo(smtp):
    response, msg = smtp.ehlo()
    assert response == 250
    assert 0
    
>>> pytest --fixtures /<path>/<root_file.py>
```

**fixture的一个优势是预先初始化应用程序对象，不必在意导入、设置和清理的细节。**
多个测试文件的fixture函数可以放在**conftest.py**中。
pytest执行流程：测试类 > 测试模块 > conftest.py > 内置插件 > 第三方插件
指定scope能够灵活的支持测试需求，例如：

```python
# conftest.py
import pytest
import smtplib

@pytest.fixture(scope="module")
def smtp():
    return smtplib.SMTP("smtp.qq.com", 587, timeout=5)
    
# test.py
def test_ehlo(smtp):
    response, msg = smtp.ehlo()
    assert response == 250
    assert b"smtp.qq.com" in msg
    assert 0
    
def test_noop(smtp):
    response, msg = smtp.noop()
    assert response == 250
    assert 0
    
```

![](http://p27x0f47q.bkt.clouddn.com/20180917204125.png)
上面的例子说明smtp被调用了两次，这是因为conftest.py中scope是module，使得每个模块只能调用一次的smtp被修改为每个测试函数调用一次。