## Pandas(II)



### Reindexing

- the <u>reindex</u> method

  - 새로운 index에 맞도록 객체를 새로 생성해준다.

    ```python
    >>> obj = pd.Series([4.5, 7.2, -5.3, 3.6], index = ['d','b','a','c'])
    >>> obj
    d    4.5
    b    7.2
    a   -5.3
    c    3.6
    dtype: float64
      
    
    >>> obj2 = obj.reindex(['a', 'b', 'c', 'd', 'e'])
    >>> obj2
    a   -5.3
    b    7.2
    c    3.6
    d    4.5
    e    NaN
    dtype: float64
    ```

  - reindex를 사용해서 새로운 값들을 index에 맞게 생성해준다.

  - 존재하던 값들을 넣었으면, 그 값에 맞게 재 배치가 되며, 없는 값은 NaN이 들어간다



- time series 같은 순차적 데이터를 reindex 하면 값이 보관 [interpolation]하거나, 채워 넣어야 할 수 있다. 

  - 이땐 Method option을 사용하면 된다
  - **ffill** 로 누락된 값을 채울 수 있다.
    - ffill 은 직전의 값으로 누락된 값을 채워넣는것 ! [forward fill 이다]

  ```python
  >>> obj3 = pd.Series(['blue', 'purple', 'yello'], index = [0, 2, 4])
  >>> obj3
  0      blue
  2    purple
  4     yello
  dtype: object
  >>> obj3.reindex(range(6), method = 'ffill')
  0      blue
  1      blue
  2    purple
  3    purple
  4     yello
  5     yello
  dtype: object
  ```

  - 기존에 0, 2, 4의 인덱스 밖에 없는데, reindex로 0~ 5까지의 인덱스를 새로 생성했고, 없는 값에 method = 'ffill'로 직전 인덱스의 값으로 채운다.

  ```python
  >>> obj3.reindex(range(6))
  0      blue
  1       NaN
  2    purple
  3       NaN
  4     yello
  5       NaN
  dtype: object
  ```

  - method = 'ffill' 을 사용하지 않은 경우이다 !



- DataFrame 상에서도 reindex가 가능하다.

  - **row index와 column index 모두 변경이 가능하다**
  - 그냥 배열로 reindex를 하면 <u>row</u>의 index가 제배치된다.

  ```python
  >>> frame = pd.DataFrame(np.arange(9).reshape((3,3)), index=['a','c','d'],
  		     columns =['Ohio', 'Texas', 'California'])
  >>> frame
     Ohio  Texas  California
  a     0      1           2
  c     3      4           5
  d     6      7           8
  ```

  - DataFrame을 선언하고, 기존 row index는 a, c,d 

  ```python
  >>> frame2 = frame.reindex(['a','b','c','d'])
  >>> frame2
     Ohio  Texas  California
  a   0.0    1.0         2.0
  b   NaN    NaN         NaN
  c   3.0    4.0         5.0
  d   6.0    7.0         8.0
  ```

  - Reindex를 하니 row index를 변경하였다.



- DataFrame의 column의 인덱스를 Reindex 하고싶다면?

  - column keyword를 사용한다

  - ```python
    >>> states = ['Texas', 'Utah' ,'California']
    >>> frame.reindex(columns = states)
       Texas  Utah  California
    a      1   NaN           2
    c      4   NaN           5
    d      7   NaN           8
    ```

  - Texas, Utah, California로 변경 Utah는 값이 없었기에, NaN 으로 채워진다

- <u>loc</u>를 쓰면 라벨링을 통해 reindexing을 더 간결하게 처리할 수 있다

  - loc 을 통해, 첫 번째 배열 [] 은 row의 인덱스를, 두 번째 배열값에 column의 인덱스를 동시에 reindex 할 수 있다.

  ```python
  >>> frame2.loc[['a', 'c','d'], states]
     Texas  Utah  California
  a      1   NaN           2
  c      4   NaN           5
  d      7   NaN           8
  ```



#### Reindex 함수의 alguments

![reindex](./readmeImg/forAfterMidTerm/pandas/reindex.png) 



