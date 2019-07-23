- [VS2019_Study C# WinForm窗体的学习记录](#vs2019-study-c--winform-------)
  * [一、**C#中将 Json 格式和对应的C#实体类格式的相互转换](#----c----json-------c-----------)


# VS2019_Study C# WinForm窗体的学习记录

## 一、**C#中将 Json 格式和对应的C#实体类格式的相互转换**

给一个在线转换网站:http://www.jsons.cn/jsontomodel/

怎么将 **`类的对象换成json字符串`** 以及  **`json字符串转换成类的对象 `**  

如何去定义相应的类的对象,对于**嵌套的json格式**相应的 **`类类型定义` **

1. <u>json格式</u> 简单认识

   > json可以简单的理解为是一个键值对的形式,类似python中的字典格式,
   >
   > ```python
   > {"key1":"value1","key2":"value2",...}
   > ```
   >
   > 给定 `key` 就可以得到相应的 `value` (value可能有多个类型) .
   >
   > ------
   >
   > json格式**允许嵌套**使用. 通常是用字符串的形式存在.

2. <u>对象</u>怎么转换成json格式

   > 通常我们拿到的是一系列键值对,需要json格式化(序列化为json形式)
   >
   > 我们怎么得到这些键值对?可以用类的成员复制得到例如:
   >
   > ```c#
   > //为了给内部数据logintime,number等赋值,这里创建类Positioninfo的对象test
   >          Positioninfo test = new Positioninfo();
   >          test.logintime = "2019-07-16";
   >          test.number = "177**";
   >          test.token = "abcd123";
   > ```
   >
   > 

3. json可以转换成的 <u>对象</u>有哪些

   > 这里的类的对象,类含义可以是**系统原有的类**,也可以是**自定义的类**类型,但是要注意<u>json转换的应该是类的对象,不是类本身!</u>
   >
   > ------
   >
   > json本身就可当做字符串进行处理,但是一般不推荐,不如转换成C#对象简单易于理解.
   >
   > ------
   >
   > 为了实际需要通常创建的是自定义的类的对象,相应的key看成类的成员变量,依据实际需要还会有成员函数存在,这些都是允许的.

4. 嵌套的json怎么定义需转换的类,并创建类的对象

   > 由2可知如何创建相应的对象
   >
   > 对于嵌套的json例如
   >
   > ```c#
   > {
   >  "status": "200",
   >  "msg": "成功",
   >  "data": {
   >      "lng": "119.29925",
   >      "lat": "32.16172"
   >  }
   > }
   > 
   > ```
   >
   > 如何去定义转换的对象的类呢?(类可以嵌套定义:smile:)
   >
   > ```c#
   > public class Data
   >  {
   >      private string mylng;
   >      private string mylat;
   >      public string lng
   >      {
   >          get { return mylng; }
   >          set { mylng = value; }
   >      }
   >      public string lat
   >      {
   >          get { return mylat; }
   >          set { mylat = value; }
   >      }
   > 
   >  }
   >  /// <summary>
   >  /// Json字符串生成C#实体类
   >  /// </summary>
   >  public class Response
   >  {
   >      private string mystatus;
   >      private string mymsg;
   >      private Data mydata;//注意这块声明前面定义内层的类的对象
   >      public string status
   >      {
   >          get { return mystatus; }
   >          set { mystatus = value; }
   >      }
   >      public string msg
   >      {
   >          get { return mymsg; }
   >          set { mymsg = value; }
   >      }
   >      /// <summary>
   >      /// 这里定义在Response类里面,类可以嵌套
   >      /// 嵌套的实体类.见http://www.jsons.cn/jsontomodel/在线转换
   >      /// </summary>
   >      public Data data
   >      {
   >          get { return mydata; }
   >          set { mydata = value; }
   >      }
   > 
   >  }
   > ```
   >
   > 为了进行嵌套,需要先定义内层的类类型public class Data{....}(因为后面要用,先在前面定义);
   >
   > 然后定义最外层类类型public class Response{......public Data data{get;set;}} ,最后创建public   List<Response> JsonToObject(string jsonString){...}方法.	List<Response>是List集合里面是Response类的对象.
   >
   > ```c#
   > public   List<Response> JsonToObject(string jsonString)
   >      {
   >          DataContractJsonSerializer serializer = new DataContractJsonSerializer(typeof(List<Response>));
   >          jsonString = "[" + jsonString + "]";
   >          MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(jsonString));
   >          object ob = serializer.ReadObject(stream);
   >          List<Response> Ls = (List<Response>)ob;
   >          return Ls;
   >      }
   > ```

部分using根据提示添加即可
