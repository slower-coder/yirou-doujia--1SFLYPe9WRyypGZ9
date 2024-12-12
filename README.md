[合集 \- LeetCode 题集(7\)](https://github.com)[1\.LeetCode题集\-1\- 两数之和08\-31](https://github.com/hugogoos/p/18389483)[2\.LeetCode题集\-2 \- 两数相加09\-05](https://github.com/hugogoos/p/18397329)[3\.LeetCode题集\-3 \- 无重复字符的最长子串09\-09](https://github.com/hugogoos/p/18405351)[4\.LeetCode题集\-4 \- 寻找两个有序数组的中位数，图文并茂，六种解法，万字讲解09\-16](https://github.com/hugogoos/p/18416625):[FlowerCloud机场](https://hushicha.org)[5\.LeetCode题集\-5 \- 最长回文子串（一）12\-08](https://github.com/hugogoos/p/18593952)[6\.LeetCode题集\-5 \- 最长回文子串之马拉车（二）12\-10](https://github.com/hugogoos/p/18596492)7\.LeetCode题集\-6 \- Z 字形变换12\-12收起
题目：将一个给定字符串 s 根据给定的行数 numRows ，以从上往下、从左到右进行 Z 字形排列。


![](https://img2024.cnblogs.com/blog/386841/202412/386841-20241211222238094-92559554.png)


![](https://img2024.cnblogs.com/blog/386841/202412/386841-20241211222230572-337668257.png)


这一题作为中等难度，下面和大家分享几种不同的解法。


# ***01***、二维矩阵模拟法


所谓二维矩阵模拟法就是首先构建一个二维矩阵，然后按照题目要求把字符串从上到下，从左到右，把字符一个一个排列到二维矩阵中，然后按行遍历二维矩阵把字符拼接起来即可。


根据上面整体思路，我们可以分为以下几个步骤：


（1）特殊情况处理，对于行数入参为1行，或者字符串长度小于行数入参，可以直接返回字符串无需处理；


（2）构建行数为行数入参，列数为字符串长度的二维矩阵，并把字符串所有字符填充至二维矩阵中；


安装题目要求应该是Z字形，也可以理解成倒N字，其实我们可以稍微变通一下，我们组成一个W形效果也是一样的


![](https://img2024.cnblogs.com/blog/386841/202412/386841-20241211222222619-1547461773.png)


经过小小的变形最终效果是一样的，虽然二维矩阵空间变大了，但是我们处理难度会大大降低。因为对于W形列索引只需要以1为步长向前移动即可，而无需像Z字形需要复杂的判断。而行索引两者处理方式相同，从第1行开始向下移动时候步长为1，当到最后1行后步长改为\-1，变为向上移动。


这就是为什么二维矩阵的列数选择为字符串长度的原因，可以大大降低处理难度。


（3）按行遍历二维矩阵，并取非空字符，拼接出结果。


具体代码如下：



```
//二维矩阵模拟法
public static string Matrix(string s, int numRows)
{
    //行数为 1 或者字符串长度小于等于行数，直接返回原字符串
    if (numRows == 1 || s.Length <= numRows)
    {
        return s;
    }
    //构建二维矩阵，用于存储 Z 字形排列的字符
    var matrix = new char[numRows, s.Length];
    //当前行索引
    var rowIndex = 0;
    //行移动步长，向下移动步长为 1 ，向上移动步长为 -1
    var rowStep = 1;
    //遍历字符串
    for (var i = 0; i < s.Length; i++)
    {
        //将当前字符放入二维矩阵中对应的位置
        matrix[rowIndex, i] = s[i];
        if (rowIndex == 0)
        {
            //如果当前行是第一行，则改变行为 1
            //代表字符移动方向为向下
            rowStep = 1;
        }
        else if (rowIndex == numRows - 1)
        {
            //如果当前行是最后一行，则改变行为 -1
            //代表字符移动方向为向上
            rowStep = -1;
        }
        // 根据行步长更新当前行的索引
        rowIndex += rowStep;
    }
    //用于存储最终结果的字符串
    var result = new StringBuilder();
    //遍历二维矩阵的行
    for (var r = 0; r < numRows; r++)
    {
        //遍历二维矩阵的列
        for (var c = 0; c < s.Length; c++)
        {
            //不为空的字符添加到结果字符串中
            if (matrix[r, c] != 0)
            {
                result.Append(matrix[r, c]);
            }
        }
    }
    // 返回最终的 Z 字形变换后的字符串
    return result.ToString();
}

```

# ***02***、行模拟法（压缩矩阵）


可以发现二维矩阵模拟法还是比较简单的，也吻合我们直观的思维习惯，但是也有一个很大的缺陷，无论是Z字形还是W字形就是浪费很多空间。


因此我们可以对二维矩阵模拟法进行改进，把其行进行压缩，因为最后我们需要以行来展示最终结果，因此我们只需要每一行构建一个字符串，相关行字符串只需要拼接至当前行字符串结尾即可，这样可以最大限度节省空间，需要多少用多少。


该方法需要解决一个核心问题——行索引计算。


再我们需要动态计算一个值时，同时需要找出其规律，要不是公式规律，要不就是周期规律，而本题是在重复Z字形，显然更符合周期规律。而如果以第一行第一个字符为起点，则终点为下个第一行第一个字符之前的一个字符。也就是第一行只有起点一个点，而最后一行只有拐点一个点。因此可以得出周期公式为：\[period \= numRows \* 2 \- 2]。


同时行数是入参是已知的，因此我们就可以通过当前字符在当前周期的哪个位置\[i % period]，再与拐点位置比较确定行索引的前进方向即可。


下面我们一起看看具体代码；



```
//行模拟法（压缩矩阵）
public static string Row(string s, int numRows)
{
    //行数为 1 或者字符串长度小于等于行数，直接返回原字符串
    if (numRows == 1 || s.Length <= numRows)
    {
        return s;
    }
    //构建字符串数组，每一个StringBuilder代表一行所有字符
    var rows = new StringBuilder[numRows];
    for (int i = 0; i < numRows; ++i)
    {
        rows[i] = new StringBuilder();
    }
    //当前行索引
    var rowIndex = 0;
    // Z 字形变换周期
    var period = numRows * 2 - 2;
    //遍历字符串
    for (int i = 0; i < s.Length; ++i)
    {
        //将当前字符添加到相对应行的末尾
        rows[rowIndex].Append(s[i]);
        //计算当前字符在周期内的那个位置
        //以最后一行的元素为分界线
        //一个周期前半部分为向下移动
        //后半部分为向上移动
        if (i % period < numRows - 1)
        {
            //向下移动
            ++rowIndex;
        }
        else
        {
            //右上移动
            --rowIndex;
        }
    }
    //把字符串数组拼接为最终结果
    var result = new StringBuilder();
    foreach (var row in rows)
    {
        result.Append(row);
    }
    return result.ToString();
}

```

# ***03***、行模拟法（代码精简）


对于有代码洁癖的我，对上一个解法做了写代码精简，主要精简了三块代码。


（1）字符串数组构建；


这里通过使用Array.ConvertAll方法实现字符串数组的声明与初始化合二为一。


（2）行索引计算方式；


这里利用周期内关于拐点对称性，直接计算出行索引。如果当前字符所在周期索引为periodIndex，则其对称索引为period – periodIndex，而行号则为两者中较小值。


（3）字符串数组拼接字符串；


而拼接方式我们可以直接使用string.Join的重载方法直接拼接。


具体代码如下：



```
//行模拟法（代码精简）
public static string RowCompact(string s, int numRows)
{
    //行数为 1 或者字符串长度小于等于行数，直接返回原字符串
    if (numRows == 1 || s.Length <= numRows)
    {
        return s;
    }
    //构建字符串数组，每一个StringBuilder代表一行所有字符
    var rows = Array.ConvertAll(new StringBuilder[numRows], _ => new StringBuilder());
    // Z 字形变换周期
    var period = 2 * numRows - 2;
    for (var i = 0; i < s.Length; i++)
    {
        //计算当前字符在周期内的那个位置
        var periodIndex = i % period;
        //获取当前行索引，利用周期内对称性，取最小值确保rowIndex不超过周期的中点
        var rowIndex = Math.Min(periodIndex, period - periodIndex);
        rows[rowIndex].Append(s[i]);
    }
    return string.Join("", rows);
}

```

# ***04***、伪直接构建


前面的方法的思路都是类似的，是先构建行数组，然后把每行字符串拼接好后，再拼接成最终结果。那我们是否可以不去构建行数组，而是直接构建最终字符串呢？


通过前面的解法我们得到Z字形变换周期，这就意味着我们可以根据周期有一次只处理一行字符的条件。而其中重点就是首行和尾行都只有一个字符，而中间的行最多有两个字符。


这样我们就可以循环行数，一行一行构建结果字符串了，具体实现代码如下：



```
//伪直接构建
public static string Build(string s, int numRows)
{
    //行数为 1 或者字符串长度小于等于行数，直接返回原字符串
    if (numRows == 1 || s.Length <= numRows)
    {
        return s;
    }
    //定义结果动态字符串
    var result = new StringBuilder();
    // Z 字形变换周期
    var period = numRows * 2 - 2;
    //遍历行
    for (var i = 0; i < numRows; ++i)
    {
        //遍历每个周期的起始位置，从 0 开始，步长为 period
        for (var j = 0; j + i < s.Length; j += period)
        {
            //当前周期的第一个字符，添加至结果中
            result.Append(s[j + i]);
            //根据 Z 字形特性，在一个周期内
            //除了第一行和最后一行只有一个字符
            //其他行则至少有一个字符，最多有两个字符
            //因此下面除了第一行和最后一行外，处理当前周期第二个字符
            if (0 < i && i < numRows - 1 && j + period - i < s.Length)
            {
                result.Append(s[j + period - i]);
            }
        }
    }
    return result.ToString();
}

```

我之所以称此法为伪直接构建，因为我认为还不够直接，我认为的直接构建是：遍历字符串后直接构建出结果。


# ***05***、真直接构建


通过前面的方法，关于Z 字形变换周期计算，关于行的计算，都已经有相应的办法，因此理论上我们是可以通过直接遍历字符串而直接拼接出结果字符串。我们可以梳理一下大致思路。


（1）首先我们构建一个字符数组，拥有存放所有字符；


（2）在这个字符数组中，我们需要动态计算出每一行字符一共站了多少字符，只有这样才能确定下一行第一个字符的起始位置，也就是相当于我们需要动态把字符数组分成n段，每一段代表一行字符；


（3）需要单独处理最后一个周期，因为最后一个周期字符是不确定的，而且它会影响每一行字符的总数，因此需要分类处理好，才能保证第（2）点的计算正确；


因此如果我们需要计算当前行之前行的所有字符总数，则可以分为两部分即完整周期\+最后一个周期。


![](https://img2024.cnblogs.com/blog/386841/202412/386841-20241211222206839-1384723749.png)


以上图为例，行数为5，最后一个周期字符可能是A\-H，即1\-8个任意个数，我们需要找到其中规律来确定最后一个周期中，每一行占了多少个字符。


如果以红色横线表示当前处理行，红色竖虚线为周期对称线，则可以把最后一个周期中最后一个字符分布在1、2、3、4四个区域中总结为以三种情况。


（1）当最后字符在1区域，则当前行之前所有行的最后一个周期字符总数为最后字符的索引；


（2）当最后字符在3、4区域，则当前行之前所有行的最后一个周期字符总数为当前行号减1；


（3）当最后字符在2区域，则当前行之前所有行的最后一个周期字符总数为最后字符的索引减去3、4区域的总字符数；


如此我们就可以一次直接构建出结果字符串，当然其中还有一些细节需要处理，比如中间行都有两个字符，而第二个字符需要格外注意，下面我们直接看看整体实现代码：



```
//真直接构建
public static string Build2(string s, int numRows)
{
    //行数为 1 或者字符串长度小于等于行数，直接返回原字符串
    if (numRows == 1 || s.Length <= numRows)
    {
        return s;
    }
    //定义结果字符数组
    var result = new char[s.Length];
    // Z 字形变换周期
    var period = 2 * numRows - 2;
    //总的周期数
    var totalPeriod = (s.Length + period - 1) / period;
    //最后一个字符的周期索引
    var lastPeriodIndex = s.Length % period;
    lastPeriodIndex = lastPeriodIndex == 0 ? (period - 1) : (lastPeriodIndex - 1);
    //遍历字符串
    for (var i = 0; i < s.Length; i++)
    {
        //当前字符串周期索引
        var periodIndex = i % period;
        //当前行索引
        var rowIndex = Math.Min(periodIndex, period - periodIndex);
        //当前字符索引，以在第几个周期为基础
        var index = (i / period);
        //处理非第一行情况
        if (rowIndex > 0)
        {
            //当前行的起始索引为此行之前所有行的所有字符总和
            //第一行总字符数为总周期数
            //第二行总字符数为分为两部分之后，
            //第一部分是(totalPeriod - 1)个周期，此部分每个周期有2个元素，
            //第二部分是最后一个周期中此行的字符个数，需要动态计算
            index += totalPeriod + (totalPeriod - 1) * 2 * (rowIndex - 1);
            //除首尾行外中间行最多有两个字符
            if (rowIndex != numRows - 1)
            {
                //第二个字符索引起始需要在第几个周期为基础上在加一个第几个周期
                index += (i / period);
            }
            //判断最后一个字符周期索引所在位置
            //动态计算此行之前最后一个周期中包含几个字符
            if (lastPeriodIndex < rowIndex)
            {
                //小于当前行数，则包含最后一个周期的所有字符
                index += lastPeriodIndex;
            }
            else if (lastPeriodIndex < period - rowIndex)
            {
                //小于当前字符对称点，则包含当前行数减1个字符
                index += rowIndex - 1;
            }
            else
            {
                //否则包含最后一个周期所有字符减去此行下面所有行最后一个周期的所有字符
                index += lastPeriodIndex - ((numRows - 1 - rowIndex) * 2 + 1);
            }
        }
        if (periodIndex <= numRows - 1)
        {
            //处理所有行的第一个字符
            result[index] = s[i];
        }
        else
        {
            //处理中间行第二个字符
            result[index + 1] = s[i];
        }
    }
    return new string(result);
}

```

***注***：测试方法代码以及示例源码都已经上传至代码库，有兴趣的可以看看。[https://gitee.com/hugogoos/Planner](https://github.com)


