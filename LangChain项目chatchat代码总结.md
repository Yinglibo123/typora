# LangChain项目chatchat代码总结

## Logging

```python
import logging

# 配置日志记录
LOG_FORMAT = "%(asctime)s - %(filename)s[line:%(lineno)d] - %(levelname)s: %(message)s"
logging.basicConfig(filename='app.log', level=logging.INFO, format=LOG_FORMAT)

# 创建日志记录器
logger = logging.getLogger()

# 记录日志
logger.debug('这是一条调试日志')
logger.info('这是一条信息日志')
logger.warning('这是一条警告日志')
logger.error('这是一条错误日志')

# 关闭日志记录器
logging.shutdown()
```

 默认的level为warning

level的优先级从低到高依次为：DEBUG,INFO,WARNING,ERROR,CRITICAL

通过设置适当的日志级别，可以控制记录哪些级别以上的日志消息，从而过滤和控制日志输出的详细程度。

这里的level为INFO，所以会记录INFO,WARNING,ERROR,CRITICAL日志。



## Process

~~~python
import multiprocessing

def my_task():
    # 子进程要执行的任务代码
    # ...
```

my_process = multiprocessing.Process(target=my_task)
#启动子进程，开始执行任务
my_process.start()  
# 等待子进程结束：如果需要等待子进程执行完毕，可以调用子进程对象的 join() 方法，阻塞主进程，直到子进程执行完毕。
my_process.join()
# my_process.terminate()  终止子进程的执行
~~~

Process的构造函数可以接收的参数：

- `group`：保留参数，暂时无用。
- `target`：指定子进程要执行的任务函数（或方法）。
- `name`：指定子进程的名称。
- `args`：传递给任务函数的位置参数，以元组形式提供。
- `kwargs`：传递给任务函数的关键字参数，以字典形式提供。
- `daemon`：指定子进程是否为守护进程。

守护进程：一种特殊类型的进程，其生命周期与主进程（也称为父进程）相关联。当主进程结束时，守护进程会随之自动终止，而不管它是否已完成执行。



在多进程编程中，每个进程都有一个唯一的进程 ID（PID），用于标识和区分不同的进程。`os.getpid()` 函数可以返回当前进程的 PID。



### async

async def fun_name():

`async def`是用于定义异步函数的语法。异步函数是一种特殊类型的函数，可以在其执行过程中暂停和恢复，以便处理其他任务而不会阻塞程序的执行。使用`async def`关键字定义的函数可以包含`await`表达式，用于等待异步操作的完成。当遇到`await`表达式时，函数会暂停执行，并将控制权返回给调用者，允许其他任务执行。一旦`await`表达式等待的操作完成，函数将恢复执行，并返回结果。

```python
import asyncio

async def hello():
    print("Hello")
    await asyncio.sleep(1)  # 模拟异步操作
    print("World")

async def main():
    await hello()

asyncio.run(main())
```



## uvicorn
uvicorn 是一个基于 asyncio 开发的一个轻量级高效的 web 服务器框架。uvicorn是一个基于FastAPI的高性能异步Web服务器。它使用了Python的`asyncio`库来实现异步处理请求，提供了一种快速、可扩展的方式来运行Web应用程序。使用uvicorn可以轻松地运行FastAPI应用程序，只需要在命令行中执行`uvicorn app:app`，其中`app`是FastAPI应用程序的实例。

uvicorn 设计的初衷是想要实现两个目标：

- 使用 uvloop和 httptools实现一个极速的 asyncio 服务器。

- 实现一个基于 ASGI(异步服务器网关接口)的最小的应用程序接口。

它目前支持 http，websockets，Pub/Sub 广播，并且可以扩展到其他协议和消息类型。





## 装饰器（@）

`@`符号被称为装饰器（Decorator）符号。装饰器是一种特殊的语法，用于修改或增强函数、类或方法的行为，而不需要对它们的源代码进行直接修改。

1. **函数装饰器：** 函数装饰器用于修改函数的行为或功能。通过在函数定义之前使用`@`符号，将装饰器应用于函数。例如，可以使用装饰器来添加日志记录、缓存、权限验证等功能。

   例子：

   ```python
   def decorator(func):
   ​    def wrapper(*args, **kwargs):
   ​        # 在调用函数前执行额外的操作
   ​        print("Before function execution")
   ​        result = func(*args, **kwargs)
   ​        # 在调用函数后执行额外的操作
   ​        print("After function execution")
   ​        return result
   ​    return wrapper
   
   @decorator
   def my_function():
   ​    print("Function execution")
   my_function()
   
   等价于：
   
   def decorator(func):
       def wrapper(*args, **kwargs):
           # 在调用函数前执行额外的操作
           print("Before function execution")
           result = func(*args, **kwargs)
           # 在调用函数后执行额外的操作
           print("After function execution")
           return result
       return wrapper
   
   def my_function():
       print("Function execution")
   
   decorated=decorator(my_function)
   decorated()
   ```



## FastChat

链接：[GitHub - lm-sys/FastChat: An open platform for training, serving, and evaluating large language models. Release repo for Vicuna and Chatbot Arena.](https://github.com/lm-sys/FastChat)

FastChat is an open platform for training, serving, and evaluating large language model based chatbots. The core features include:

- The weights, training code, and evaluation code for state-of-the-art models (e.g., Vicuna).
- A distributed multi-model serving system with web UI and OpenAI-compatible RESTful APIs.

To serve using the web UI, you need three main components: ==web servers== that interface with users, ==model workers== that host one or more models, and a ==controller== to coordinate the webserver and model workers. You can learn more about the architecture [here](https://github.com/lm-sys/FastChat/blob/main/docs/server_arch.md).

### FastChat Server的架构

![server arch](https://github.com/lm-sys/FastChat/raw/main/assets/server_arch.png)

### steps to launch a local OpenAI API server for LangChain.

-  launch the controller

```python
python3 -m fastchat.serve.controller
```

- LangChain uses OpenAI model names by default, so we need to assign some faux OpenAI model names to our local model. Here, we use Vicuna as an example and use it for three endpoints: chat completion, completion, and embedding. `--model-path` can be a local folder or a Hugging Face repo name. See a full list of supported models [here](https://github.com/lm-sys/FastChat/blob/main/README.md#supported-models).

```python
python3 -m fastchat.serve.model_worker --model-names "gpt-3.5-turbo,text-davinci-003,text-embedding-ada-002" --model-path lmsys/vicuna-7b-v1.3
```

- Finally, launch the RESTful API server

```python
python3 -m fastchat.serve.openai_api_server --host localhost --port 8000
```



## FastApi

FastAPI是一个现代化、高性能的Python Web框架，用于构建快速、可扩展的Web应用程序和API。它结合了Python类型注解和异步编程，提供了简洁的API设计、自动化的文档生成和高性能的特点。

### 使用步骤

1. 导入 FastAPI（from fastapi import FastAPI）；
2. 创建一个 app 实例（app = FastAPI()）；
3. 编写一个路径操作装饰器（如 @app.get("/")）；
4. 编写一个路径操作函数（如上面的 def root(): ...）；
5. 定义返回值运行开发服务器（如 uvicorn main:app --reload）；



### 一个例子

1. 创建一个 Python 文件（例如 `main.py`），并编写以下代码：

```python
from fastapi import FastAPI

# 创建 FastAPI 应用程序实例
app = FastAPI()

# 定义一个路由和对应的处理函数
@app.get("/")
def read_root():
    return {"Hello": "World"}

# 运行应用程序
if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

在上述代码中，我们导入 FastAPI 并创建了一个 FastAPI 应用程序实例 `app`。然后，我们定义了一个根路由（`"/"`）并指定了对应的处理函数 `read_root`。这个处理函数会返回一个字典作为 JSON 响应。

2. 运行应用程序：使用以下命令来运行 FastAPI 应用程序。

```
uvicorn main:app --reload
```

这将启动一个服务器并运行 FastAPI 应用程序。`main:app` 表示运行 `main.py` 文件中的 `app` 对象，`--reload` 参数用于在代码发生更改时自动重新加载应用程序。

3. 测试 API：在浏览器或使用 API 测试工具（如 Postman 或curl）中访问 `http://localhost:8000/`，将获得一个 JSON 响应 `{"Hello": "World"}`。

### 注意点

- `@app.get()` 是一个装饰器，用于将一个函数注册为处理 HTTP GET 请求的路由处理函数。可以将一个函数与特定的路由路径和请求方法（GET 方法）相关联。当客户端向该路径发送 GET 请求时，FastAPI 将会调用被装饰的函数来处理这个请求。

  在上面的例子中，`@app.get("/")` 装饰器将 `read_root()` 函数与根路径 `/` 和 GET 请求方法关联起来。当客户端向 `/` 发送 GET 请求时，FastAPI 将会调用 `read_root()` 函数来处理这个请求。

- 路径操作函数的返回值：FastAPI 默认使用 JSONResponse 类来处理响应，并将 Python 对象自动序列化为 JSON 字符串。这样，客户端就能够以 JSON 格式获取响应数据。如果需要返回其他类型的数据，如 HTML、纯文本、二进制数据等，可以使用相应的返回类型，如 `HTMLResponse`、`PlainTextResponse`、`FileResponse` 等。



## \*args与\**kwargs

`*args` 允许接收可变数量的位置参数：将传入的多个数据收集到一个元组中。

```python
def my_function(*args):
    for arg in args:
        print(arg)

my_function(1, 2, 3)  # 输出：1 2 3
my_function('apple', 'banana', 'orange')  # 输出：apple banana orange
```

 `**kwargs` 允许接收可变数量的关键字参数，将数据收集到一个字典中。

```python
def my_function(**kwargs):
    for key, value in kwargs.items():
        print(key, value)

my_function(name='Alice', age=25)  # 输出：name Alice  age 25
my_function(city='New York', country='USA')  # 输出：city New York  country USA
```



## Steamlit库

streamlit是一种用于构建交互式Web应用程序的开源Python库。它提供了简单易用的语法和功能，使开发人员能够快速创建数据驱动的应用程序和可视化界面。使用streamlit，您可以通过编写简洁的Python脚本来展示数据、创建交互式图表和构建用户界面。

### 创建一个标题

```python
import streamlit as st

st.info("这是一个一级标题")
st.write("# 标题")

st.info("这是一个一级标题")  # 可以直接使用markdown的语法
'''# 标题'''

st.info("这是一个一级标题")
st.markdown("# 标题")

st.info("这是一个一级标题")
st.title("标题")

st.info("这是一个二级标题")
st.header("二级标题")

st.info("这是一个三级标题")
st.subheader("三级标题")

dict={"key":"value"}
dict   #可以直接在网页中显示文本，变量值，对象
```

魔术命令是指：

- 使用st.write(*args,**kwargs)
- 直接写变量名或者对象名，或者用引号包裹markdown语法



st.text(body:str)

st.caption(body:str)



### 代码块

1. st.code(body,language="python")

   code="""import streamlit as st

   

   st.info("这是一个一级标题")

   st.write("# 标题")"""

   st.code(code)

2. Markdown语法：

   """

   \```python

   import streamlit as st

   

   st.info("这是一个一级标题")

   st.write("# 标题")

   \```

   """



