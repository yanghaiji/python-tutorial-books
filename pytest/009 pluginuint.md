## Pytest 实用插件



### pytest-rerunfailures

`pytest-rerunfailures`是用于当我们的测试用例由于某些原因，进行请求的再次尝试

- 安装

  ```
  pip install pytest-rerunfailures
  ```

- 案例

  通过@pytest.mark.flaky 进行失败重试

  - reruns 请求尝试的次数
  - reruns_delay 间隔的时间

  ```python
  
  import pytest
  
  
  @pytest.mark.parametrize('a,b,result', [
      (1, 1, 3),
      (2, 2, 4),
      (100, 100, 200),
      (0.1, 0.1, 0.2),
      (-1, -1, -2)
  ], ids=['int', 'int', 'bignum', 'float', 'fushu'])  # 参数化
  @pytest.mark.flaky(reruns=6, reruns_delay=2)
  def test_add(a, b, result):
      # cal = Calculator()
      assert result == a + b
  
  
  if __name__ == '__main__':
      # "--reruns 5 --reruns-delay 1",
      pytest.main(['-vs', "rerunfailures_demo.py"])
  
  ```

  `@pytest.mark.flaky(reruns=6, reruns_delay=2)` 当失败后我们尝试6次，每次间隔2秒，结果如下

  ```
  collecting ... collected 5 items
  
  rerunfailures_demo.py::test_add[int0] RERUN
  rerunfailures_demo.py::test_add[int0] RERUN
  rerunfailures_demo.py::test_add[int0] RERUN
  rerunfailures_demo.py::test_add[int0] RERUN
  rerunfailures_demo.py::test_add[int0] RERUN
  rerunfailures_demo.py::test_add[int0] RERUN
  rerunfailures_demo.py::test_add[int0] FAILED
  rerunfailures_demo.py::test_add[int1] PASSED
  rerunfailures_demo.py::test_add[bignum] PASSED
  rerunfailures_demo.py::test_add[float] PASSED
  rerunfailures_demo.py::test_add[fushu] PASSED
  
  ================================== FAILURES ===================================
  _______________________________ test_add[int0] ________________________________
  
  a = 1, b = 1, result = 3
  
      @pytest.mark.parametrize('a,b,result', [
          (1, 1, 3),
          (2, 2, 4),
          (100, 100, 200),
          (0.1, 0.1, 0.2),
          (-1, -1, -2)
      ], ids=['int', 'int', 'bignum', 'float', 'fushu'])  # 参数化
      @pytest.mark.flaky(reruns=6, reruns_delay=2)
      def test_add(a, b, result):
          # cal = Calculator()
  >       assert result == a + b
  E       assert 3 == 2
  E         +3
  E         -2
  
  rerunfailures_demo.py:26: AssertionError
  =========================== short test summary info ===========================
  FAILED rerunfailures_demo.py::test_add[int0] - assert 3 == 2
  ==================== 1 failed, 4 passed, 6 rerun in 12.20s ====================
  ```

  

###  pytest-assume

正常情况下一条用例如果有多条断言，一条断言失败了，其他断言就不会执行了，而使用pytest-assume可以继续执行下面的断言

- 安装

  ```
  pip install pytest-assume
  ```

- 案例

  ```python
  import pytest
  
  
  def test_assume():
      print('登录操作')
      pytest.assume(1 == 2)
      print('搜索操作')
      pytest.assume(2 == 2)
      print('加购操作')
      pytest.assume(3 == 2)
  
  
  if __name__ == '__main__':
      pytest.main(["-vs", "assume_demo.py"])
  ```

- 运行结果

  ```
  collecting ... collected 1 item
  
  assume_demo.py::test_assume 登录操作
  搜索操作
  加购操作
  FAILED
  
  ================================== FAILURES ===================================
  _________________________________ test_assume _________________________________
  
  tp = <class 'pytest_assume.plugin.FailedAssumption'>, value = None, tb = None
  
      def reraise(tp, value, tb=None):
          try:
              if value is None:
                  value = tp()
              if value.__traceback__ is not tb:
  >               raise value.with_traceback(tb)
  E               pytest_assume.plugin.FailedAssumption: 
  E               2 Failed Assumptions:
  E               
  E               assume_demo.py:17: AssumptionFailure
  E               >>	pytest.assume(1 == 2)
  E               AssertionError: assert False
  E               
  E               assume_demo.py:21: AssumptionFailure
  E               >>	pytest.assume(3 == 2)
  E               AssertionError: assert False
  
  C:\python_tools\python3\lib\site-packages\six.py:718: FailedAssumption
  =========================== short test summary info ===========================
  FAILED assume_demo.py::test_assume - pytest_assume.plugin.FailedAssumption: 
  ============================== 1 failed in 0.10s ==============================
  ```

  

### pytest-ordering

　正常情况下，用例默认执行顺序是自上而下的，对于一些有上下文依赖关系的用例，可是通过 pytest-ordering 来设置执行顺序，当然，通过setup、teardown和fixture来解决也是可以的

- 安装

  ```
   pip install pytest-ordering
  ```

- 案例

  ```python
  import pytest
  
  @pytest.mark.run(order=2)
  def test_foo():
      assert True
  
  @pytest.mark.run(order=1)
  def test_bar():
      assert True
  ```

### pytest-dependency

使用该插件可以标记一个testcase作为其他testcase的依赖，当依赖项执行失败时，那些依赖它的test将会被跳过。

- 安装 

  ```
   pip install pytest-dependency
  ```

- 案例

  使用方法： 用 @pytest.mark.dependency()对所依赖的方法进行标记，使用@pytest.mark.dependency(depends=["test_name"])引用依赖,test_name可以是多个。

  ```python
  import pytest
  
  @pytest.mark.dependency()
  def test_01():
      assert False
  
  @pytest.mark.dependency(depends=["test_01"])
  def test_02():
      print("执行测试2")
  ```

- 运行结果

  ```
  collecting ... collected 2 items
  
  dependency_demo.py::test_01 FAILED
  dependency_demo.py::test_02 SKIPPED (test_02 depends on test_01)
  
  ================================== FAILURES ===================================
  ___________________________________ test_01 ___________________________________
  
      @pytest.mark.dependency()
      def test_01():
  >       assert False
  E       assert False
  
  dependency_demo.py:17: AssertionError
  =========================== short test summary info ===========================
  FAILED dependency_demo.py::test_01 - assert False
  ======================== 1 failed, 1 skipped in 0.11s =========================
  ```


### pytest-xdist

- 平常我们功能测试用例非常多时，比如有1千条用例，假设每个用例执行需要1分钟，如果单个测试人员执行需要1000分钟才能跑完
- 当项目非常紧急时，会需要协调多个测试资源来把任务分成两部分，于是执行时间缩短一半，如果有10个小伙伴，那么执行时间就会变成十分之一，大大节省了测试时间
- 为了节省项目测试时间，10个测试同时并行测试，这就是一种分布式场景

　分布式执行用例的原则：

- 用例之间是独立的，没有依赖关系，完全可以独立运行用例执行没有顺序要求，随机顺序都能正常执行每个用例都能重复运行，运行结果不会影响其他用例

- 安装

  ```
  pip install pytest-xdis
  ```

- 案例

  　pytest -n 2 (2代表2个CPU)，pytest -n auto

  - nauto：可以自动检测到系统的CPU核数；从测试结果来看，检测到的是逻辑处理器的数量，即假12核使用auto等于利用了所有CPU来跑用例，此时CPU占用率会特别高

  ```python
  import pytest
  
  
  @pytest.mark.parametrize('a,b,result', [
      (1, 1, 3),
      (2, 2, 4),
      (100, 100, 200),
      (0.1, 0.1, 0.2),
      (-1, -1, -2)
  ], ids=['int', 'int', 'bignum', 'float', 'fushu'])  # 参数化
  @pytest.mark.flaky(reruns=6, reruns_delay=2)
  def test_add(a, b, result):
      # cal = Calculator()
      assert result == a + b
  
  
  if __name__ == '__main__':
      # "--reruns 5 --reruns-delay 1",
      pytest.main(['-vs', "rerunfailures_demo.py", "-n 2"])
  
  ```

- 结果

  ```
  plugins: allure-pytest-2.9.45, assume-2.4.3, dependency-0.5.1, forked-1.4.0, html-3.1.1, metadata-1.11.0, ordering-0.6, rerunfailures-10.2, xdist-2.5.0
  gw0 I / gw1 I
  [gw0] win32 Python 3.8.9 cwd: C:\source_code\python_work\python-tutorial\pytests\plug_in_unit
  [gw1] win32 Python 3.8.9 cwd: C:\source_code\python_work\python-tutorial\pytests\plug_in_unit
  [gw0] Python 3.8.9 (tags/v3.8.9:a743f81, Apr  6 2021, 14:02:34) [MSC v.1928 64 bit (AMD64)]
  [gw1] Python 3.8.9 (tags/v3.8.9:a743f81, Apr  6 2021, 14:02:34) [MSC v.1928 64 bit (AMD64)]
  gw0 [5] / gw1 [5]
  
  scheduling tests via LoadScheduling
  
  rerunfailures_demo.py::test_add[int1] 
  rerunfailures_demo.py::test_add[int0] 
  [gw1] PASSED rerunfailures_demo.py::test_add[int1] 
  rerunfailures_demo.py::test_add[float] 
  [gw1] PASSED rerunfailures_demo.py::test_add[float] 
  rerunfailures_demo.py::test_add[fushu] 
  [gw1] PASSED rerunfailures_demo.py::test_add[fushu] 
  [gw0] RERUN rerunfailures_demo.py::test_add[int0] 
  rerunfailures_demo.py::test_add[int0] 
  [gw0] RERUN rerunfailures_demo.py::test_add[int0] 
  rerunfailures_demo.py::test_add[int0] 
  [gw0] RERUN rerunfailures_demo.py::test_add[int0] 
  rerunfailures_demo.py::test_add[int0] 
  [gw0] RERUN rerunfailures_demo.py::test_add[int0] 
  rerunfailures_demo.py::test_add[int0] 
  [gw0] RERUN rerunfailures_demo.py::test_add[int0] 
  rerunfailures_demo.py::test_add[int0] 
  [gw0] RERUN rerunfailures_demo.py::test_add[int0] 
  rerunfailures_demo.py::test_add[int0] 
  [gw0] FAILED rerunfailures_demo.py::test_add[int0] 
  rerunfailures_demo.py::test_add[bignum] 
  [gw0] PASSED rerunfailures_demo.py::test_add[bignum] 
  
  ================================== FAILURES ===================================
  _______________________________ test_add[int0] ________________________________
  [gw0] win32 -- Python 3.8.9 C:\python_tools\python3\python.exe
  
  a = 1, b = 1, result = 3
  
      @pytest.mark.parametrize('a,b,result', [
          (1, 1, 3),
          (2, 2, 4),
          (100, 100, 200),
          (0.1, 0.1, 0.2),
          (-1, -1, -2)
      ], ids=['int', 'int', 'bignum', 'float', 'fushu'])  # 参数化
      @pytest.mark.flaky(reruns=6, reruns_delay=2)
      def test_add(a, b, result):
          # cal = Calculator()
  >       assert result == a + b
  E       assert 3 == 2
  E         +3
  E         -2
  
  rerunfailures_demo.py:26: AssertionError
  =========================== short test summary info ===========================
  FAILED rerunfailures_demo.py::test_add[int0] - assert 3 == 2
  ==================== 1 failed, 4 passed, 6 rerun in 13.41s ====================
  
  Process finished with exit code 0
  
  ```

  