## 有关js for循环里的作用域问题

**1、js中作用域只有函数作用域和全局作用域，在函数体内使用var 定义的变量，会被提到函数开始处进行定义，作用域为整个函数,常见的误区如下**

      var a=[];
      for(var i = 0;i<10;i++){
          var q = i;
          a[i]=function(){console.log(q)}
      }
      a[0]()
      
 其中，由于for循环并不是一个函数体，所以for循环中定义的变量q和i是作用域for循环所在的函数体，和a同级，
 i++ 和  q=i 并不是重新定义变量，只是重复赋值，最终循环结束，i = 10,q=9;
 由于function(){console.log(q)} 并不是立即执行，所以这里的q一直是存储的内存引用，最终所有的a[i]()都是输出 9
 不过，在es6中新增了let命令声明变量，用法和var类似，不过let所声明的变量，只在let命令所在的代码块有效果，for循环的计数器中就很适合let命令
 
        var a=[];
           for(let i = 0;i<10;i++){
               let q = i;
               a[i]=function(){console.log(q)}
           }
           a[6]()    //这里会输出   6  let声明的变量仅在块级作用域有效，所以这里的i只在本轮循环有效果，每次循环的i其实都是一个新的变量
 **2、js中的for循环遇到延时器的问题**
 
 当你在for循环里写if判断，再加延时器或者定时器时，一定要保存当前的i的值，再做处理，否则你拿到的i的值会是for循环里最大的那个
 
    for (var i = 0; i < 10; i++) {
    			if(i == 5){
    				setTimeout(aa,2000);
    				function aa(){
    					console.log( "i="+i);  //10
    				}
    			}
    		}
 js读取代码是从上向下读取的，当它读取到i满足if语句的时候并不是停止了，还会继续做循环判断；而此时if语句里面是一个延时器，当延时器的延时时间结束要调用aa函数的时候，for循环已经循环结束，而此时的i已经变为10
 
 所以打印出来i的值就会是10；
 
 那么怎么解决这个问题呢？看代码
    
     var j = null;  
             for (var i = 0; i < 10; i++) {  
                 if(i == 5){  
                     j = i;  
                     setTimeout(aa,2000);  
                     function aa(){  
                         console.log( "i="+j);  
                     }  
                 } 
             } 
 
 这样打印出来的就是我们想要的结果了，没错就是5；
 
 原理就是当满足if语句时，我们用一个变量把当前i的值保存下来.
 
 **3、JS中for循环里面的闭包问题的原因及解决办法**
 
  先看一个普通的for循环
  
  ```
      function foo(){
         var arr = [];
         for(var i=0;i<8;i++){
           arr[i]=i;
         } 
         return arr;
      }
      
      console.log(foo());   //[0, 1, 2, 3, 4, 5, 6, 7]
```
  
  for循环里包含闭包函数
  
      function foo(){
         var arr=[];
         
         for(var i=0;i<8;i++){
            arr[i]=function(){
            reurn i
            }
         }
         return arr;
      }
      
      var f = foo();
      console.log(f[0]());