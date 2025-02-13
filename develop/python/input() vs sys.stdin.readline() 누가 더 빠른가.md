# input()과 sys.stdin.readline()

파이썬에서 입력작업을 할때 쓰이는 두 함수이다. 코딩테스트를 풀다보면 대량의 데이터를 입력하는 경우에,
input() 함수는 속도가 느리기에 readline을 사용하라는 얘기들이 많다. 그렇기에 이에 대해서 한번 조사하고 학습한 내용을 정리해본다.

## input() 함수가 sys.stdin.readline() 보다 느린 이유

input 함수 내부에서 어떤 일이 일어나는지 확인하였다.

1. 프롬프트(prompt) 출력 처리
   코드에서 prompt를 명시적으로 쓰든 안 쓰든, 파이썬 내부에서는 prompt 출력과 버퍼 flush가 일어난다.
2. 감사(auditing) 이벤트 발생
   파이썬은 3.8 이후부터 보안/로그를 위해서 builtins.input (입력 전)과 builtins.input/result(입력 후) 이벤트를 발생시킨다. 그리고 보안훅 (sys.addaudithook)을 단 환경에선 입력 호출 사실과 결과가 감시/기록이 가능하다.
3. readline 모듈 연동
   'import readline' (GNU Readline 라이브러리. sys.stdin.readline 아님)가 로딩되어 있으면, 대화형 모드에서 화살표 키로 편집,히스토리 탐색등이 가능하다.
4. 표준입력에서 문자열 읽기
   Cpython 소스에서 pyOS_Readline 함수를 호출해 내부적으로 한줄 읽어온다.
5. 개행문자 (\n) 제거
   입력후에 끝에 붙은 엔터(\n)를 제거한다.

이렇게 input 함수 내부에선 입력 외에도 여러 과정을 거치기에 입력 데이터가 많아질경우 오버헤드가 커져서 속도가 느려진다.

## sys.stdin.readline() 함수

- sys.stdin(표준 입력 스트림)에서 그냥 한줄 읽어서 문자열로 변환
- input 함수에서 일어나는 여러 과정들 없음
- 문자열끝에 개행문자가 그대로 포함되기에 따로 제거해야함
- 그냥 입력 기능외에 다른 것이 없기에 훨씬 빠름

내용을 조사해보니 이러한 이유가 있었다. input 함수는 그저 입력만 하는 함수라고 단순히 생각했었는데, 내부에선 생각보다 다양한 추가로직들이 존재했다.
그저 원인만 알게 된것에서 더 나아가, 한번 실제 소스코드에서 어떤 부분이 이러한 작업을 하는지 알아보았다.

이 소스코드는 CPython의 소스이다. 파이썬 엔진은 c언어로 작성되어 있다.

```c
// 아래의 이 builtin_input_impl 함수가 실제 파이썬 input 함수의 진입점이다.
builtin_input_impl(PyObject *module, PyObject *prompt)
/*[clinic end generated code: output=83db5a191e7a0d60 input=159c46d4ae40977e]*/
{
    // 함수의 진입부분 	_PySys_GetAttr: sys.stdin, sys.stdout, sys.stderr 객체를 C 레벨에서 받아옴
    PyThreadState *tstate = _PyThreadState_GET();
    PyObject *fin = _PySys_GetAttr(tstate, &_Py_ID(stdin));
    PyObject *fout = _PySys_GetAttr(tstate, &_Py_ID(stdout));
    PyObject *ferr = _PySys_GetAttr(tstate, &_Py_ID(stderr));

    ...

    // 실제 2번에 해당하는 감사 이벤트 부분이다.
    if (PySys_Audit("builtins.input", "O", prompt ? prompt : Py_None) < 0) {
        return NULL;
    }

    /* First of all, flush stderr */
    if (_PyFile_Flush(ferr) < 0) {
        PyErr_Clear();
    }
    ...
    tmp = PyObject_CallMethodNoArgs(fin, &_Py_ID(fileno));
    ...
    tty = fd == fileno(stdin) && isatty(fd);
    ...

    // 3번에 해당하는 부분이다. 터미널이면 → GNU Readline으로 “라인 편집, 히스토리” 기능을 사용
    //아니면(파일에서 입력받는 상황 등) → “그냥 한 줄 읽기”로 fallback TTY를 체크하는 과정도 오버헤드가 발생한다.
    if (tty) {
        // tty 일경우 내부에서도 많은 오버헤드가 발생한다.
        ...
        // 이제 실제로 문자열을 읽는 부분이다.
        s = PyOS_Readline(stdin, stdout, promptstr);
        ...
        len = strlen(s);
        ...
        len--; /* strip trailing '\n' */
        ...
        // 이곳에서 개행문자 제거
        result = PyUnicode_Decode(s, len, stdin_encoding_str, stdin_errors_str);
        ...
        if (result != NULL) {
            // 여기서 감사이벤트 builtins.input/result가 발생
            if (PySys_Audit("builtins.input/result", "O", result) < 0) {
                return NULL;
            }
        }
        return result;
    }

    ...

    // tty가 아닌경우 처리
    /* Fallback if we're not interactive */
    if (prompt != NULL) {
        if (PyFile_WriteObject(prompt, fout, Py_PRINT_RAW) != 0)
            return NULL;
    }
    if (_PyFile_Flush(fout) < 0) {
        PyErr_Clear();
    }
    // tty가 아닐경우 이곳에서 표준입력으로 한줄 가져온다.
    return PyFile_GetLine(fin, -1);
}
```

이렇게 소스코드 내부에서 보면은

1. sys.stdin / sys.stdout / sys.stderr 확인 (None이면 오류)
2. 감사 이벤트(builtins.input) 발생
3. stderr flush
4. stdin이 TTY인지 체크(fileno, isatty)
5. 만약 TTY면
   GNU Readline(PyOS_Readline)으로 한 줄 입력
   개행 문자 제거
   감사 이벤트(builtins.input/result) 발생
   결과 반환
6. TTY가 아니면
   prompt 있으면 출력, flush
   PyFile_GetLine으로 한 줄 읽기
   결과 반환

이러한 다양한 작업들이 이루어진다. c언어로 되어있고 내부 소스코드는 매우 복잡하기에 읽고 이해하는 것이 매우 어려웠다.
하지만 그저 sys.stdin.readline 함수가 빠르다. 이렇게 끝이 아닌 소스코드까지 파고 들어가 이러한 이유로 input 함수가 늦는다는 것을 알게되었다.
마지막으로 이곳에서 작성한 내용은 내가 개인적으로 조사하면서 알게되고 이해한 내용을 적은 것이기에 틀린 내용이 있을수도 있음을 밝힙니다.

## reference

- [파이썬 input](https://docs.python.org/3/library/functions.html#input)
- [sys.stdin](https://docs.python.org/3/library/sys.html#sys.stdin)
- [파이썬 소스코드 input 함수 부분](https://github.com/python/cpython/blob/main/Python/bltinmodule.c)
