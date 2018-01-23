# PyTest_Cheatsheet
**Most important PyTest knowledge at a glance**

### Basic Testing
```python
def test_demotest():
  assert True
  assert 1 == 1
  assert 2>=1
  somelist = [1, 2]
  assert 1 in somelist
```

### Options

``` -k 'test_demotest' ``` Runs all tests whose names contain 'test_demotest'</br>
``` -m 'demomarker and not othermarker' ``` Runs all tests marked with "demomarker" which do not have a "othermarker" on them </br>
``` -x ``` Exits on forst error </br>
``` --maxfail=2 ``` Exits after 2 errors </br>
``` --lf ``` Reruns only **l**ast **f**ailed test </br>
``` --ff ``` Reruns the **l**ast **f**ailed tests first </br>
``` -v ``` Inrease verbosity </br>
``` -q ``` Decrease verbosity (quiet) </br>
``` --duration=10 ``` Shows slowest 10 tests </br>
``` --collect-only ``` Collects tests, but does not execute them (to check if the options are set right) </br>
``` -h ``` Shows help </br>


### Marking

#### Custom Marker

```python
@pytest.mark.demomarker
def test_demotest():
  assert True
```
Running the test with ``` -m 'demomarker' ``` will only execute tests which have the ```@pytest.mark.demomarker```-Marker

#### Skipping Test
Skippes test and shows reason when using the verbose ```-v``` option for the output:
```python
@pytest.mark.skip(reason='This is the reason for skipping this test')
def test_demotest():
  assert True
```

Skippes test if some **requirement** is met (like checking the version of the class, V1.0 does not support the tested feature but V1.1 does):
```python
@pytest.mark.skipif(someClass.__version__<'1.0')
def test_demotest():
  assert True
```

#### Expect Failure
```python
@pytest.mark.xfail()
def test_demotest():
  assert False
```
Expect failure until some **requirement** is met (like checking the version of the class):
```python
@pytest.mark.xfail(someClass.__version__<'1.0',reason='not suported until V1.1')
def test_demotest():
  assert False
```

#### Parameterized Tests
Using a list of strings to **run a test multiple times with different parameters**:
```python
@pytest.mark.parametrize('strings',['string1','string2'])
def test_demotest(strings):
  assert 'string1' == strings
```

Using a list of tuples to run a test multiple times with different parameters:
```python
@pytest.mark.parametrize('city,code',[('zurich',8050),('basel',4001)])
def test_demotest(city,code):
  assert city == 'zurich'
  assert code == 8050
```

#### Fixtures
Fixtures are bits of code that run **before** and **after** the tests. They can be used to **prepare sets of data** which are needed by multiple tests or to **initialize and close a database** connection:
```python
@pytest.fixture()
def demodata():
    return 'demodata'

def test_some_data(demodata):
    assert demodata == 'demodata'
```

Sharing Fixtures among **multiple files**: define fixture in the **conftext.py** file to make it available in this directory and all subdirectories.

Fixtures with DB-connections:
```python
@pytest.fixture()
def someFixture():
    startDB()
    
    yield
    
    stopDB()
```
Fixtures can be used by other fixtures. Also multiple fixtures can be used.

The **Scope** of a fixture can be definded additionaly:
```@pytest.fixture(scope='function')``` Runs once per each test function (**default**)
```@pytest.fixture(scope='class')``` Runs once per test class
```@pytest.fixture(scope='module')``` Runs once per module
```@pytest.fixture(scope='session')``` Runs once per session



