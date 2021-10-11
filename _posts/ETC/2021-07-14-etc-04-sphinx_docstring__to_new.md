---
title: "[ETC] Sphinx - Docstring Syntax / Napoleon"
excerpt: 
last_modified_at: 2021-07-14

categories:
  - ETC

tags:
  - python
  - sphinx
  - documentation
  - docstring
  - rst
  - syntax
  - extension
  - napoleon

---

![sphinx-version-4.1.0](https://img.shields.io/badge/Sphinx-v4.1.0-lightgreen.svg)

# Docstring Syntax

- `returns` \=\= `return`

- `rtype` \=\= `returns type`

- `Args` \=\= `Parameters`

- . . .

<br><br>

## reStructuredText

```
:param <DataType> <Parameter1>: <Explanation>
:param <DataType> <Parameter2>: <Explanation>
:returns: <Explanation>
:rtype: <DataType>
:raises <ErrorType>: <Explanation>
```

<br>

### Example

```python
def example_sphinx(a, b):
    """This returns sum of a and b

    Some Explanation

    :param int a: Explanation of parameter a
    :param int b: Explanation of parameter b
    :returns: Sum of a and b (a + b)
    :rtype: int
    :raises TypeError: Occurs when ...

    """
    return a + b
```

<br>

### Notice

```
:param <DataType> <Parameter1>: <Explanation>
```

위의 코드는 아래와 같이 변경 가능하다.

```
:param <Parameter1>: <Explanation>
:type <Parameter1>: <DataType>
```

<br><br>

## Google Style

```
Args:
  <Parameter1> (<DataType>): <Explanation>
  <Parameter2> (<DataType>): <Explanation>

Returns:
  <DataType>:
  <Explanation>
  
Raises:
  <ErrorType>
    <Explanation>
```

<br>

### Example

```python
def example_google(a, b):
    """This returns sum of a and b

    Some Explanation

    Args:
      a (int): Explanation of parameter a
      b (int): Explanation of parameter b

    Returns:
      int:
      Sum of a and b (a + b)
      
    Raises:
      TypeError
        Occurs when ...

    """
    return a + b
```

<br><br>

## NumPy Style

```
Parameters
----------
<Parameter1> : <DataType>
  <Explanation>
<Parameter2> : <DataType>
  <Explanation>
  
Retruns
-------
<DataType>
  <Explanation>
  
Raises
------
<ErrorType>
  <Explanation>
```

<br>

### Example

```python
def example_numpy(a, b):
    """This returns sum of a and b

    Some Explanation

    Parameters
    ----------
    a : int
      Explanation of parameter a
    b : int
      Explanation of parameter b

    Returns
    -------
    int
      Sum of a and b (a + b)
      
    Raises
    ------
    TypeError
      Occurs when ...

    """
    return a + b
```

<br><br>

# Napoleon

Google Style 또는 NumPy Style Docstring 을 Sphinx 가 parse 가능하게 해준다.  
즉 reStructuredText 형식을 사용하지 않더라도 동일한 결과물을 빌드할 수 있다.  

<br>

## Add Extensions

`conf.py`

```python
extensions = [
    '...',
    'sphinx.ext.napoleon',
]
```

<br>

## Result

Napoleon 에 의해 Google Style 과 NumPy Style 모두  
아래 이미지와 같이 동일한 결과가 되어 나타난다.  

![py-sphinx_docstring+result](./images/etc-04-sphinx_docstring+result.png?raw=true){: width="400px"}  

- example 함수는 `example` 패키지의 `first_module` 에 작성  
- 테마는 `sphinx_rtd_theme` 을 적용한 상태  

<br><br>

# Reference

- [Sphinx Documentation - Support for NumPy and Google style docstrings](https://www.sphinx-doc.org/en/master/usage/extensions/napoleon.html)
- [Sphinx Documentation - Example Google Style Python Docstrings](https://www.sphinx-doc.org/en/master/usage/extensions/example_google.html)
- [Sphinx Documentation - Example NumPy Style Python Docstrings](https://www.sphinx-doc.org/en/master/usage/extensions/example_numpy.html)
