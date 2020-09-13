#### 倒计时（将时间差转换成 n天m小时x分y秒）
    /*
     * 参数timeDiff——距今时间差值
     */
     function start_time_diff(timeDiff)
     {
         var m = timeDiff % (3600*24);
         var d = (timeDiff - m)/(3600*24);
         var t = m%3600;
         var h = (m - t)/3600;
         var s = t%60;
         var i = (t - s)/60;
         var timeStr = '';
         if(d > 0) timeStr += d + '天';
         if(d > 0 || h > 0) timeStr += h + '小时';
         if(d > 0 || h > 0 || i > 0) timeStr += i + '分';
         timeStr += s + '秒';
         return timeStr;
     }
     
     /*
      * 由于html页面进入后台非激活状态后，进程休眠，造成倒计时不准
      * 解决倒计时不准的方法是做矫正
      */
      var standardTime;
      var originalTime = parseInt(new Date().getTime() / 1000);
      var timer = setInterval(dtime, 1000);
      
      function dtime()
      {
          originalTime++;
          standardTime = parseInt(new Date().getTime() / 1000);
          if(standardTime - originalTime > 0){
            var adjustVal = standardTime - originalTime;
          }else{
            var adjustVal = 0;
          }
          originalTime = standardTime;
          var timeStmp = $("#djs_num").val() - 1 - adjustVal;
          var timeStr = start_time_diff(timeStmp);
          $("#djs_str").html(timeStr);
          $("#djs_num").val(timeStmp);
      }