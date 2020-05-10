### jquery笔记

#### Jquery的事件绑定
    用js半路添加的元素是无法在Jquery中生效的，解决办法：
    1、在添加的元素中直接用onclick等定义触发事件；
    2、刷新Jquery绑定：
    $(document).ready(function(){
        $(func函数()); 或 func函数(); //初次绑定
        $('添加元素').click(function(){
            .....生成新元素.....
            $('该类元素').unbind('click等事件'); //解除原绑定
            $(func函数()); 或 func函数(); //刷新绑定
        });
    });
    function func函数(){
        ........ //定义绑定
    }

#### Jquery常用的元素选定方法
    //该元素的直接父元素
        $('某元素').parent() <=> $('某元素:parent')
    //直接子元素
        $('某元素').children([子元素]) <=> $('某元素 > 子元素')
    //该元素全部子元素，包括间接
        $('某元素').find(子元素) <=> $('某元素 子元素')
    //包含(直接或间接)指定子元素的某元素自身
        $('某元素').has(子元素) <=> $('某元素:has(子元素)')
    //后方[全部]同级元素，不包括自身
        $('某元素').nextAll([...]) / next([...])
    //前方[全部]同级元素，不包括自身
        $('某元素').prevAll([...]) / prev([...])
    //同类元素中的第一个
        $('某元素').first() <=> $('某元素:first')
    //同类元素中的末一个
        $('某元素').last() <=> $('某元素:last')
    //同类元素中的第n个（n从0起计）
        $('某元素').eq(n) <=> $('某元素:eq(n)')
    //同类元素中的第0、2、4....个
        $('某元素:even')
    //同类元素中的第1、3、5....个
        $('某元素:odd')
    //同时选定多个元素
        $('元素A，元素B')

#### Jquery生成文档元素
    1、添加元素
        $('某元素').after(新内容) //在指定元素之后添加
        $('某元素').before(新内容) //在指定元素之前添加
        $('某元素').append(新内容) //在指定元素内部末尾添加
        $('某元素').prepend(新内容) //在指定元素内部开头添加
    2、移动元素
        $('元素A').insertAfter('元素B') //将元素A移动到B后面
        $('元素A').insertBefore('元素B') //将元素A移动到B前面
        $('元素A').appendTo('元素B') //将元素A移动到B内部末尾
        $('元素A').prependTo('元素B') //将元素A移动到B内容开头
    3、包装元素
        $('某元素').wrap('<em></em>') //包装单个元素
        $('某组元素').wrapAll('<em></em>') //包装整组元素
        $('某元素').wrapInner('<em></em>') //包装单个元素内部内容
    4、复制黏贴元素
        $('某元素').clone().insertAfter('其他元素')
    5、替换元素
        $('某元素').replaceWith(新内容) //将"元素"替换成"新内容"
    6、清除元素
        $('某元素').remove();

#### Jquery中each循环遍历的使用
    （注意不能用break跳出）
    1、遍历数组对象
        $.each(数组对象, function(key, value){
            //key即键名，value即键值
        });
    2、遍历文档元素
        $('某组元素').each(function([idx]){
            //$(this)即为其中每一个元素
        });

#### 鼠标移入、移出hover事件
    情形一、
        $('元素').hover(
            function(){....}, //鼠标移入行为
            function(){....} //鼠标移出行为
        );
    情形二、
        $('元素').hover(function(){
            $(this).toggleClass('临时样式');
        });

#### 仅触发一次
    $(".cls").one('click', function(){
        ......对该元素的click触发仅有一次，第二次点击无效
    });
    注、针对同一个元素的再次点击无效，但是同为class="cls"的其他元素仍可生效。

#### 文档元素textarea、radio、checkbox、select的赋值取值
    1、文本域
        赋值或取值（注意、文本域中的内容换行是\r\n）
        $('.textarea').val()
    2、单、复选按钮
        执行选定
        $('.radio:eq(n)').attr('checked', true)
        撤销选定
        $('.radio:eq(n)').attr('checked', false)或.removeAttr('checked')
        判断是否选定
        if($('.radio:eq(n)').attr('checked'))
    3、下拉菜单
        执行或判断当前选定
        $('.select').val()

#### 关于prop在单选、复选、下拉框中的使用
    attr返回的是字符串'checked'或'selected'
    prop返回true或false
    
    1）单选、复选框
        赋值：$('input:eq(n)').prop('checked', true/false);
        判断：if($('input:eq(n)').prop('checked')==true)
    2）下拉框
        赋值：$('select > option:eq(n)').prop('selected', true);
            或 $('select').val('...');
        判断：if($('select').prop('selectedIndex')==0、1、2...)
            或 if($('select').val()=='...')

#### 阻止事件冒泡，以及阻止href跳转
    $('某元素').事件(function(event){
        event.stopPropagation(); //阻止事件冒泡
        event.preventDefault(); //阻止href跳转
    });

#### Jquery对象转换成DOM元素
    $('元素').get(n) //返回DOM元素 <=> 相当于document.getElementById('...')

#### 事件触发后对元素的判断
    $('某元素').事件(function(event){
        //event.target 返回DOM元素，可以是冒泡后的对象，包含this
        //$(event.target) 返回jquery对象，包含$(this)
    
        //判断被触发对象
        1）if(event.target.tagName=='DIV') <=> if($(event.target).is('div'))
        2）if($(this).hasClass('...'))
    });