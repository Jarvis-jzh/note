# Servlet 生命周期

​	servlet有良好的生存期的定义，包括如何加载、实例化、初始化、处理客户端请求以及如何被移除。这个生存期由javax.servlet.Servlet接口的init,service和destroy方法表达。



## 1、加载和实例化
​	容器负责加载和实例化一个servlet。实例化和加载可以发生在引擎启动的时候，也可以推迟到容器需要该servlet为客户请求服务的时候。
首先容器必须先定位servlet类，在必要的情况下，容器使用通常的Java类加载工具加载该servlet，可能是从本机文件系统，也可以是从远程文件系统甚至其它的网络服务。容器加载servlet类以后，它会实例化该类的一个实例。需要注意的是可能会实例化多个实例，例如一个servlet类因为有不同的初始参数而有多个定义，或者servlet实现SingleThreadModel而导致容器为之生成一个实例池。

## 2、初始化
​	servlet加载并实例化后，容器必须在它能够处理客户端请求前初始化它。初始化的过程主要是读取永久的配置信息，昂贵资源（例如JDBC连接）以及其它仅仅需要执行一次的任务。通过调用它的init方法并给它传递唯一的一个（每个servlet定义一个）ServletConfig对象完成这个过程。给它传递的这个配置对象允许servlet访问容器的配置信息中的名称－值对（name-value）初始化参数。这个配置对象同时给servlet提供了访问实现了ServletContext接口的具体对象的方法，该对象描述了servlet的运行环境。



### 2.1初始化的错误处理
​	在初始化期间，servlet实例可能通过抛出UnavailableException 或者 ServletException异常表明它不能进行有效服务。如果一个servlet抛出一个这样的异常，它将不会被置入有效服务并且应该被容器立即释放。在此情况下destroy方法不会被调用因为初始化没有成功完成。在失败的实例被释放后，容器可能在任何时候实例化一个新的实例，对这个规则的唯一例外是如果失败的servlet抛出的异常是UnavailableException并且该异常指出了最小的无效时间，那么容器就会至少等待该时间指明的时限才会重新试图创建一个新的实例。



### 2.2、工具因素
​	当工具（注：根据笔者的理解，这个工具可能是应用服务器的某些检查工具，通常是验证应用的合法性和完整性）加载和内省（introspect）一个web应用时，它可能加载和内省该应用中的类，这个行为将触发那些类的静态初始方法被执行，因此，开发者不能假定只要当servlet的init方法被调用后它才处于活动容器运行状态（active container runtime）。作为一个例子，这意味着servlet不能在它的静态（类）初始化方法被调用时试图建立数据库连接或者连接EJB容器。



## 3、处理请求
​	在servlet被适当地初始化后，容器就可以使用它去处理请求了。每一个请求由ServletRequest类型的对象代表，而servlet使用ServletResponse回应该请求。这些对象被作为service方法的参数传递给servlet。在HTTP请求的情况下，容器必须提供代表请求和回应的HttpServletRequest和HttpServletResponse的具体实现。需要注意的是容器可能会创建一个servlet实例并将之放入等待服务的状态，但是这个实例在它的生存期中可能根本没有处理过任何请求。



### 3.1、多线程问题
​	容器可能同时将多个客户端的请求发送给一个实例的service方法，这也就意味着开发者必须确保编写的servlet可以处理并发问题。如果开发者想防止这种缺省的行为，那么他可以让他编写的servlet实现SingleThreadModel。实现这个类可以保证一次只会有一个线程在执行service方法并且一次性执行完。容器可以通过将请求排队或者维护一个servlet实例池满足这一点。如果servlet是分布式应用的一部分，那么，那么容器可能在该应用分布的每个JVM中都维护一个实例池。如果开发者使用synchronized关键字定义service方法(或者是doGet和doPost)，容器将排队处理请求，这是由底层的java运行时系统要求的。我们强烈推荐开发者不要同步service方法或者HTTPServlet的诸如doGet和doPost这样的服务方法。



### 3.2、处理请求中的异常
​	servlet在对请求进行服务的时候有可能抛出ServletException或者UnavailableException异常。ServletException表明在处理请求的过程中发生了错误容器应该使用合适的方法清除该请求。UnavailableException表明servlet不能对请求进行处理，可能是暂时的，也可能是永久的。如果UnavailableException指明是永久性的，那么容器必须将servlet从服务中移除，调用它的destroy方法并释放它的实例。如果指明是暂时的，那么容器可以选择在异常信息里面指明的这个暂时无法服务的时间段里面不向它发送任何请求。在这个时间段里面被被拒绝的请求必须使用SERVICE_UNAVAILABLE (503)返回状态进行响应并且应该携带稍后重试（Retry-After）的响应头表明不能服务只是暂时的。容器也可以选择不对暂时性和永久性的不可用进行区分而全部当作永久性的并移除抛出异常的servlet。



### 3.3线程安全
​	开发者应该注意容器实现的请求和响应对象（注：即容器实现的HttpServletRequest和HttpServletResponese）没有被保证是线程安全的，这就意味着他们只能在请求处理线程的范围内被使用，这些对象不能被其它执行线程所引用，因为引用的行为是不确定的。



## 4、服务结束
​	容器没有被要求将一个加载的servlet保存多长时间，因此一个servlet实例可能只在容器中存活了几毫秒，当然也可能是其它更长的任意时间（但是肯定会短于容器的生存期）当容器决定将之移除时（原因可能是保存内存资源或者自己被关闭），那么它必须允许servlet释放它正在使用的任何资源并保存任何永久状态（这个过程通过调用destroy方法达到）。容器在能够调用destroy方法前，它必须允许那些正在service方法中执行的线程执行完或者在服务器定义的一段时间内执行（这个时间段在容器调用destroy之前）。一旦destroy方法被调用，容器就不会再向该实例发送任何请求。如果容器需要再使用该servlet，它必须创建新的实例。destroy方法完成后，容器必须释放servlet实例以便它能够被垃圾回收。  