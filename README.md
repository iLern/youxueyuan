## 增加Chrome插件
下载： https://hsmustard.github.io/youxueyuan/chrome/fuckyxy.crx
@author FuckSky.


## 本脚本的功能：
- 优学院的题目可以直接显示正确答案
- 填空题可以自动填充。
- 自动添加5.00x播放速度。（觉得不够可以继续往下看）
<!--more-->
## 使用方法
具有 **开发者工具（俗称F12）** 的浏览器
在 **console** 粘贴以下内容：
```javascript
var script = document.createElement('script');
script.src = "https://hsmustard.github.io/youxueyuan/yxy.min.js";
document.getElementsByTagName('head')[0].appendChild(script);
```


**然后在 console 输入**

**`showAnswer();` 是显示答案**

**`fillBlanks();` 是自动填充*填空题*答案。**

**`quickVideo(10);` 是手动设定视频播放速度，括号内填加速的倍数**
*非开发者到此就读完了~*

----------

## 具体源码
```javascript
function removeDuplicatedItem(arr) {  //去掉重复的id
   for(var i = 0; i < arr.length-1; i++){
       for(var j = i+1; j < arr.length; j++){
           if(arr[i]==arr[j]){
              arr.splice(j,1);
              j--;
           }
       }
   }
   return arr;
}

function fillBlanks(){
	var ansarr = [];
	var idList = [];
	var re =[];
	$('.blank-input').each(function(k, v) {
	    var id = $(v).parent().parent().parent().parent().parent().parent().parent().attr('id');
	    id = id.replace('question', '');
	    idList.push(id);
	});
	idList = removeDuplicatedItem(idList);
	// console.log(idList);
	$(idList).each(function(k, id) {
		$.ajax({
			async: false,
			type : "get",
			url : 'https://api.ulearning.cn/questionAnswer/' + id,
			datatype : 'json',
			success : function(result) {
				re.push(result.correctAnswerList);
			}
			});
	});
	console.log(re);

	$(re).each(function(k1, v1) {
		if(v1.length == 1){
			ansarr.push(v1[0]);
		}else{
			$(v1).each(function(k2, v2) {
				ansarr.push(v2);
			});
		}
	});
	// console.log(ansarr);
	$('.blank-input').each(function(k,v){
		console.log(v);
		$(v).val(ansarr.shift());
	});
}

function showAnswer(){
	var sqList = [];
	var re = [];
	$('.question-wrapper').each(function(k,v){
		var id = $(v).attr('id');
		sqList.push(id.replace('question',''));
	});
	// console.log(sqList);
	$(sqList).each(function(k, id) {
		$.ajax({
			async: false,
			type : "get",
			url : 'https://api.ulearning.cn/questionAnswer/' + id,
			datatype : 'json',
			success : function(result) {
				re.push(result.correctAnswerList);
			}
			});
	});
	// console.log(re);
	var an = [];
	$(re).each(function(k,v){
		an.push(v.join(','));
	});
	var t = $('.question-wrapper').find('.question-title-html');
	t.each(function(k,v){
		// console.log(v);
		$(v).after('<span style="color:red;">答案：'+an.shift()+'</span>');
	});
}

function quickVideo(speed = 2){
     $('video').each(function(k,v){
         v.playbackRate = speed;
         console.log('视频速率为 '+speed+' x');
     });
}

function addSpeed5x(){
    var lastIn = $('.mejs__speed-selector-input').last();
    lastIn.val('5.00');
    lastIn.next().text('5.00x');
}
addSpeed5x();
```
