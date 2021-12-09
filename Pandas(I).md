##  Pandas (I)

> Pandas 는 Python에서 쉽고 빠르게 사용할 수 있는 <u>데이터 분석</u> 도구를 제공한다
>
> NumPy와 비슷한 경향이 있지만, 데이터 분석에 초점이 맞춰져 있다.
>
> NumPy는 배열 계산에 초점이 맞춰져 있다.



- What is Pandas ?
- Introduction to pandas Data Structures
  - Series
  - DataFrame
  - Index Objects



### What is Pandas

- Pandas는 테이블 상에서 빠르고 쉽게 <u>data cleaning과 분석 [analysis]</u> 가 가능한 자료구조와 데이터 조작 툴을 제공한다.
- Pandas는 주로 NumPy, SciPy 등 유명한 라이브러리 들과 함께 사용된다.
- 배열 기반 연산 스타일을 사용해서, 배열 기반의 함수나 반복문 없이 데이터를 처리하는 방안을 제공한다.



##### Pandas와 NumPy의 차이점

> Pandas는 Heterogeneous data [이중형태의 데이터]를 다루는데 초점이 되어있고, NumPy는 Homogeneous data, 즉 동일한 종류의 데이터를 다루는데 초점이 맞춰져있다.



### Introduction to pandas Data Structures

- Pandas의 자료구조인 **<u>Series & DataFrame</u>** 에 대해 알아보자

- ```python
  import pandas as pd
  from pandas import Series, DataFrame
  ```



### Series

- Series는 1차원 배열 객체를 의미한다.

  - 연속적인 **homogeneous data**와 **index** 라고 불리는 **data labels** 을 포함한다.

  - 가장 간단한 series 객체는 array로부터 가져올 수 있다

  - ```python
    >>> import pandas as pd
    >>> from pandas import Series, DataFrame
    >>> obj = pd.Series([4, 7, -5, 3])
    >>> obj
    0    4
    1    7
    2   -5
    3    3
    dtype: int64
    ```

  - 여기서 0 부터 3 까지가 index, 4, 7, -5, 3이 Series 의 value이다.

- The value and index attributes - **Series에서 Value와 index를 따로 추출하고 싶다면**

  - the value attribute

    - ```python
      >>> obj.values  # series의 value를 반환
      array([ 4,  7, -5,  3])
      ```

  - the index attributes

    - ```python
      >>> obj.index
      RangeIndex(start=0, stop=4, step=1)
      ```

    - 여기서 기본 인덱스를 갖고 있지만, obj.index로 인덱스를 지정할 수 있다

    - ```python
      >>> obj.index = ['A', 'C', 'E', 'F']
      >>> obj
      A    4
      C    7
      E   -5
      F    3
      dtype: int64
      ```

    - 이러면, ACEF가 obj 의 index attribute, 4, 7, -5, 3이 value attributes



- Pandas는 각각의 데이터를 지칭하는 인덱스를 지정할 수 있다

  - obj.index

  - Series 객체를 선언하는 시점에 지정할 수 있음.

  - ```python
    >>> obj2 = pd.Series([4, 6, -5, 3], index = ['A', 'C', 'E', 'F'])
    >>> obj2
    A    4
    C    6
    E   -5
    F    3
    dtype: int64
    ```

  - 또한 단일 값을 선언하거나, 인덱스를 지정해서 값을 가져올 수 있으며, 변경도 가능하다.

  - ```python
    >>> obj2['A']
    4
    >>> obj2['E'] = 6 # 값 변경
    >>> obj2[['E','A','F']] #원하는 인덱스 값들만 불러오기 
    E    6
    A    4
    F    3
    dtype: int64
    ```



- Series에서 Index-value는 연결이 되어있다. **그리고 이는 보존이 되어있다.**
  - <img src="./readmeImg/forAfterMidTerm/Series.png" alt="Series" style="zoom:50%;" /> 
  - Boolean 배열을 사용한 값 걸러네기, Scalar, Numpy를 사용해도
  - Index-value는 연결되있고, value에만 영향을 준다



- Series를 쉽게 이해하기 위해

  - **Series는 고정된 길이의 정렬된 Dictionary 라고 할 수 있다.** 

  - index - value가 맵핑이 되므로, Pandas의 dictionary와 유사하다

  - Series는 Python의 dictionary를 대체해서 사용하거나, 실제로 Python의 Dictionary를 불러서 Series를 불러서 생성할 수 있다.

    - ```python
      >>> sdata = {'A' : 10, 'T': 102, 'O': 123} # Python Dictionary !
      >>> obj3 = pd.Series(sdata) # dictionary를 이용해서 생성할 수 있다.
      >>> obj3
      A     10
      T    102
      O    123
      dtype: int64
      ```

  

- Python의 dictionary 자료를 사용해 Series 객체를 생성하면, 생성된 Series 객체의 Index는 dictionary의 key 값이 순서대로 들어간다.
  - Series 객체의 Index를 다른 인덱스 리스트로 지정하는게 가능하다.
  - <img src="./readmeImg/forAfterMidTerm/Series2.png" alt="Series2" style="zoom:50%;" /> 
  - State 배열을 index로 넣으면, California에는 NaN, 기존 sdata에 존재하지 않는 index..
  - Nan = Not a Number 



-  누락 된 값인 NaN값을 찾는 방법
  - isnull 과 notnull 함수를 사용해서 Pandas에서 누락된 값을 찾을 수 있다.
  - <img src="./readmeImg/forAfterMidTerm/isnullnotnull.png" alt="isnullnotnull" style="zoom:50%;" />
  - **isnull은 NaN 값을 True로 리턴**
  - **notnull은 NaN 값을 False로 리턴**
  - pd.isnull() 로 사용할 수도 있고, 객체.isnull() 로도 사용할 수 있다



- **Series 객체간의 산술 연산에서 인덱스 라벨을 자동 정렬해주는 기능**

  - <img src="./readmeImg/forAfterMidTerm/seriesSort.png" alt="seriesSort" style="zoom:50%;" /> 
  - 인덱스에 매칭되는 값들 끼리 연술이 된다.. !!

  

- **Name Attribute** [중요]

  - Series 객체와, 인덱스는 name attribute를 갖고있다

  - ```python
    >>> obj4 = pd.Series([4, 6, -5, 3], index = ['A', 'C', 'E', 'F'])
    >>> obj4
    A    4
    C    6
    E   -5
    F    3
    dtype: int64
    >>> obj4.name= 'population'
    >>> obj4.index.name = 'alphabet'
    >>> obj4
    alphabet
    A    4
    C    6
    E   -5
    F    3
    Name: population, dtype: int64
    ```

  - obj4.index.name 을 사용해서 인덱스에 명을 지정할 수 있고, obj에 이름을 지정해줄 수 있다.





### DataFrame

> excel 과 같은 spreadsheet 형태의 자료구조 !
>
> 직사각형의 테이블에 여러개의 컬럼이 존재하면, 서로 다른 종류의 데이터를 저장할 수 있다.
>
> RDB 형태와 비슷하다 - row index 와 column index 둘 다 갖고있다.
>
> 1차원 형태의 자료구조가 아닌, 2차원 블록에 저장이 된다 !



##### DataFrame Construct [생성]

- 가장 흔한 방법은, **같은 길이의 List에 담긴 Dictionary나 numpy 배열을 사용하는 것**

  - ```python
    >>> data = {'state' : ['Ohio', 'Ohio', 'Nevada', 'Nevada'], 'year' : [2000, 2001, 2000, 2002], 'pop' : [1.5, 1.7, 3.6, 2.9]}
    >>> data
    {'state': ['Ohio', 'Ohio', 'Nevada', 'Nevada'], 'year': [2000, 2001, 2000, 2002], 'pop': [1.5, 1.7, 3.6, 2.9]}
    >>> frame = pd.DataFrame(data)
    >>> frame
        state  year  pop
    0    Ohio  2000  1.5
    1    Ohio  2001  1.7
    2  Nevada  2000  3.6
    3  Nevada  2002  2.9
    ```

  - 각 각의 row는 동일한 위치에 존재하는 각등의 쌍으로 되어있고

  - column 값은 dictionary의 value로 이루어져 있다.



- head method()
  - 엄청나게 큰 데이터 프레임이 존재하면, 유용하게 사용할 수 있다.
  - 























