###iostat

<pre><code>
iostat -d
Linux 2.6.32-504.el6.x86_64 (vm90) 	11/14/2016 	_x86_64_	(4 CPU)

Device:            tps   Blk_read/s   Blk_wrtn/s   Blk_read   Blk_wrtn
sda               0.90         1.33        17.21    3092610   40126184

</code></pre>

>tps：该设备每秒的传输次数（Indicate the number of transfers per second that were issued to the device.）即iops。“一次传输”意思是“一次I/O请求”。多个逻辑请求可能会被合并为“一次I/O请求”。“一次传输”请求的大小是未知的。

>kB_read/s：每秒从设备（drive expressed）读取的数据量；

>kB_wrtn/s：每秒向设备（drive expressed）写入的数据量；

>kB_read：读取的总数据量；

>kB_wrtn：写入的总数量数据量；