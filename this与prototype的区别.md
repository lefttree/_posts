title: this与prototype的区别
date: 2015-01-05 19:15:53
tags: node&js
---

* this可以访问私有成员（用var声明的变量）私有方法（直接function或者内部），prototype无法访问
* Js首先查找自身的静态属性，方法，再查找上下文中可以访问的属性或方法，最后才查找prototype，谁先谁优先
* prototype比this要节约分配的空间，因为prototype有一个指向父类的指向，而this，每次实例化时都要分配存储空间


		var Copy = function(){
			var d = "123";
			this._log = function(){
				console.log(d);
			}
			this.log = function(){
				console.log('yes');
			}
		}
		Copy.prototype.log = function(){
			console.log('no');
		}
		var c = new Copy();
		c.log();

