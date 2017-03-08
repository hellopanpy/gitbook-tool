###iotop

类似top，实时监控io负载

<pre><code>
Total DISK READ: 0.00 B/s | Total DISK WRITE: 0.00 B/s
  TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN     IO>    COMMAND                                                                                                                       
  256 be/3 root        0.00 B/s    0.00 B/s  0.00 %  0.00 % udevd -d
    1 be/4 root        0.00 B/s    0.00 B/s  0.00 %  0.00 % init
    2 be/4 root        0.00 B/s    0.00 B/s  0.00 %  0.00 % [kthreadd]
    3 rt/4 root        0.00 B/s    0.00 B/s  0.00 %  0.00 % [migration/0]
</code></pre>


>DISK READ:磁盘读取速率

>DISK WRITE：磁盘写入速率

>SWAPIN

>IO:应用程序占用IO时间的百分比，主要关注这一项    
