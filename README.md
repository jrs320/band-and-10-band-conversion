# band-and-10-band-conversion
10进制转2-26进制，用26个字母表述。

> 几点说明

- **从1开始表述，比从0开始表述难度加大。**<br>
- **存在问题，代码实现逻辑是对的，但当数字很大时，js里面数字之间的`+-*/`运算会出现精度不准确问题，回头再写个js运算util。**<br>
- **这里只实现最多26进制转换，因为用的26个大写英文字母表述，当然可以利用更多的字符来表述更多的进制，比如`a-zA-Z0-9`这个就可以表述62进制。**<br>

> 规则

    1 => A
    2 => B
    3 => C
    ...
    26 => Z
    27 => AA
    28 => AB
    29 => AC
    ...
    52 => AZ
    53 => BA
    ...

> 代码

```javascript
/**
* 把一个10进制的正整数转换成2-26之间进制数，并用26个字母显示输出
* @param 
* num(Number) 	: 需要转换的10进制正整数
* mode(Number) 	: 转换的进制数（2-26）
* @return(String) ：26个英文字母表示的进制字符串
* @author : jrs
*/
function convert (num, mode) {

	// 只转换正整数，从1开始表述，
	// 比如我们熟悉的2，8，10，16进制表示的数字都是从0开始，这里从1开始（从1开始会增加难度）
	num = parseInt(num);
	num = isNaN(num) ? 0 : Math.abs(num);
	if(num == 0)
	{
		console.log("传入参数num非法，请传入正整数");
		return;
	}
	
	// 这里只用26个字母来表示，所以只支持2-26之间的进制值
	mode = mode || 26;
	mode = parseInt(mode);
	mode = isNaN(mode) ? 0 : mode;
	if(mode < 2 || mode > 26)
	{
		console.log("传入参数mode非法，支持2-26之间的进制值");
		return;
	}
	
	var str = [];
	var code = " ABCDEFGHIJKLMNOPQRSTUVWXYZ".split("");
	
	// 取模26的余数
	var r = num % mode;
	if(r == 0)
	{
		r = mode;
	}
	// 先把字符串的最后一位存起来
	str.push(code[r]);
	
	// 去除余数	
	num = num - r;
	// 剩下的num值一定是mode的整数倍
	(function(num){
	
		if(num <= 0)
		{
			return;
		}
		
		// 从最高位开始，跟我们熟悉的10进制数，2进制数对比思考
		// 比如十进制 332 = 3 * Math.pow(10,2) + 3 * Math.pow(10,1) + 2 * Math.pow(10,0)
		// 相应的     CCB = 3 * Math.pow(26,2) + 3 * Math.pow(26,1) + 2 * Math.pow(26,0)
		
		// 计算当前位底数 
		var highestNum = mode;
		while(true)
		{
			if(highestNum < num)
			{
				highestNum = highestNum * mode;
			}
			else
			{
				highestNum = highestNum / mode;
				highestNum = highestNum == 1 ? mode : highestNum
				break;
			}
		}

		// 计算当前位的系数
		var n = 1;
        var lastNum = highestNum;
		for(var i = 1; i <= mode; i++)
		{
			var temp = i * highestNum;
			if(temp < num)
			{
				n = i;
				lastNum = temp;
			}
			else if(temp == num)
			{
				// 两位的时候
				if(highestNum == mode)
				{
					n = i;
					lastNum = temp;
				}
				// 大于两位时，要注意，因为向高位进位要本位满位以后，
				// 显示的时候要高位系数减一，留给低位满位显示。
				else
				{
					lastNum = temp - highestNum;
				}
				break;
			}
			else
			{
				break;
			}
		}
		
		// n就是当前位的系数，code[n]找到该系数对应的转换字母
		str.push(code[n]);
		
		// 递归计算剩余的位数
		arguments.callee(num - lastNum);
		
	})(num);
	
	// 把最先存起的最后一位字母放到最后
	str.push(str.shift());
	str = str.join("");
	
	console.log(str);
	
	return str;
}

convert(3 * Math.pow(26,2) + 3 * Math.pow(26,1) + 2 * Math.pow(26,0));
```
