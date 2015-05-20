title: 使用Worker实现一个多线程数据缓存模块
date: 2015-05-20 16:24:18
tags: JavaScript
---

Worker是一个可以在后台运行的任务，并提供了一个JavaScript接口让我们来使用它。Worker能够被轻松的创建，还能向它的创建者发送消息。 你只要调用 Worker() 构造函数，指定一个需要运行在 worker 线程内的脚本，就能够很容易的创建一个 worker。

Worker这个API的使用非常的简单

* postMessage()  向worker内部发送消息
* terminate() 立即停止当前的worker
* onmessage 一个消息事件，worker内部向前台发送消息 
* onerror 一个错误事件，运行环境出错时，worker内部向前台发送消息

属性

* message 一个可读的错误消息
* filename 错误所在的文件名
* lineno 错误所在的文本行号 

JavaScript Client Code

	var workerQueue = [];
	var cacheSystem = function(){
		var isWork 		= !!Worker;
		var dataSource 	= {};
		var contains 	= [];
		var lock		= false;
		var count 		= 0;
		var getDash     = function(){
			var dash 		= localStorage.getItem('scanUseDashDataSource');
			var dashData 	= dash ? JSON.parse(dash).data : contains;
			return dashData; 
		}
		return	{
			allObject:function(){
				var d = localStorage.getItem('scanUseModalDataSource');
				return d ? d = JSON.parse(d).data : null;
			},
			objectForKey:function(key){
				return this.allObject()[key];
			},
			allKeys:function(){
				var o = this.allObject();
				return o ? Object.keys(o) : contains;
			},
			allValues:function(){
				var o = this.allObject();
				if(!o){
					return contains;
				}
				var c = [];
				for(var k in o){
					c.push(o[k].data);
				}
				return c;
			},
			allDash:function(){
				return getDash();
			},
			setObject:function(value){
				var s 			= this;
				var metadata 	= this.allObject() || {};
				metadata[value] = {"data":null,"work":false,"error":false};
				var dashData 	= this.allDash();	
				//如果有网络，提交给后台线程去处理网络请求
				window.networkState = 1;
				if(isWork && window.networkState > 0){
					var worker = new Worker('fetchThread.js');
					worker.addEventListener('message',function(evt){
						console.log(evt);
						var obj = s.allObject();
						var sp 	= evt.data.split('@icepy->');
						var tag = sp[0];
						var dat = sp[1];
						var o   = obj[tag];
						var r   = JSON.parse(dat);
						if(o){
							//把后台得到的数据添加到data中
							o.data 	= r;
							//标记work已经完成
							o.work  = true;
							//标记执行线程锁
							lock 	= false;
							if(r.error){
								o.error = true;
							}
							localStorage.setItem('scanUseModalDataSource',JSON.stringify({"data":obj}));
							workerQueue[count][2] = true;
							count++;
							var work = workerQueue[count];
							if(work){
								if(work[2]){
									var i = count++;
									var f = true;
									do{
										var _work = workerQueue[i];
										if(!_work[2]){
											_work[1].postMessage(_work[0]);
											f = false;
											count = i;
											break;
										}
										i++;
									}while(f);
								}else{
									work[1].postMessage(work[0]);
								}
							}else{
								workerQueue.length = 0;
								count = 0;
							}
						}
						
					});
					worker.addEventListener('error',function(evt){
						console.log('错误信息处理');
					});
					//把线程加入队列
					workerQueue.push([value,worker,false]);
					if(!lock){
						lock = true;
						worker.postMessage(value);
					}
				}
				if(!(dashData.indexOf(value) > -1)){
					dashData.push(value);
				}
				localStorage.setItem('scanUseDashDataSource',JSON.stringify({"data":dashData}));
				localStorage.setItem('scanUseModalDataSource',JSON.stringify({"data":metadata}));
			},
			setValue:function(key,value){
				var metadata = this.allObject();
				metadata[key].data = value.concat();
				metadata[key].work = true;
				localStorage.setItem('scanUseModalDataSource',JSON.stringify({"data":metadata}));
			},
			removeObjectForKey:function(key){
				var metadata 	= this.allObject();
				var dash 		= this.allDash();
				dash.splice(dash.indexOf(key),1);
				this.pop(key);
				delete metadata[key];
				localStorage.setItem('scanUseDashDataSource',JSON.stringify({"data":dash}));
				localStorage.setItem('scanUseModalDataSource',JSON.stringify({"data":metadata}));
			},
			repeat:function(v){
				var d = this.allKeys();
					i = 0;
				if(!d.length){
					return false;
				}else{
					var lock = false;
					if(d.indexOf(v) > -1){
						lock = true;
					}
					return lock;
				}
			},
			pop:function(key,d){
				if(!d){
					d = this.allObject();
				}
				if(isWork){
					var i = 0;
					var e = workerQueue.length;
					if(e > 0){
						for(;i<e;i++){
							var work = workerQueue[i];
							if(work && work[0] === key){
								work[2] = true;
								work[1].terminate();
								break;
							}
						}
					}
				}
			},
			isWork:isWork
		}
	}();

JavaScript Thread Code

	addEventListener('message',function(evt){
		var parameter 	= evt.data;
		var xhr 		= new XMLHttpRequest();
		var headers		= {};
		xhr.onreadystatechange = function(e){
   			if(xhr.readyState === 4){
   				clearInterval(abortTimeout);
   				if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
   					postMessage(parameter+'@icepy->'+xhr.responseText);
   					close();
   				}
   			}
   		}
    	xhr.open('POST','http://127.0.0.1:8001/',true);
   		xhr.send(parameter);
   		var abortTimeout = setInterval(function(){
   			xhr.abort();
   			postMessage(parameter+'@icepy->'+'{"error":"timeout"}');
   			close();
   		},1000*13);

   		xhr.onerror = function(e){
   			postMessage(parameter+'@icepy->'+'{"error":"notnetwork"}');
   			close();
   		} 	
	});
	


阅读资料

* [https://developer.mozilla.org/zh-CN/docs/Web/API/Worker](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker)