1. 准备工作，下载ubuntu镜像，安装vmware(略)
2. 虚拟机配置如下图：

　[![HBZz2n.png](https://s4.ax1x.com/2022/02/12/HBZz2n.png)](https://imgtu.com/i/HBZz2n)

3. 打开虚拟机网络管理器配置如下：

　[![HBeasP.png](https://s4.ax1x.com/2022/02/12/HBeasP.png)](https://imgtu.com/i/HBeasP)

4. 打开控制面板找到网络连接，打开网络管理器的虚拟机网卡并配置如下：
　 [![HBmGwT.md.png](https://s4.ax1x.com/2022/02/12/HBmGwT.md.png)](https://imgtu.com/i/HBmGwT)

5. 开始安装操作系统，依次点击，这里只列举需要修改的地方
   + 网卡配置
   
   [![HBmX1s.md.png](https://s4.ax1x.com/2022/02/12/HBmX1s.md.png)](https://imgtu.com/i/HBmX1s)
   
   + ssh软件包需要勾选
   
   [![HBn33d.md.png](https://s4.ax1x.com/2022/02/12/HBn33d.md.png)](https://imgtu.com/i/HBn33d)
 
 6. 等待安装结束后重启系统进入，测试网络和dns是否生效
 　　[![HBngbV.md.png](https://s4.ax1x.com/2022/02/12/HBngbV.md.png)](https://imgtu.com/i/HBngbV)
 
 7. 测试与本地PC的连通性，发现并不能通信，此时的情况是，虚拟机可以上外网，并能正常dns,但是不能和宿主机通信，虚拟机不能和VMnet8通信,PC不能和gateway通信，但是可以通VMnet
 　　[![HBuRsI.md.png](https://s4.ax1x.com/2022/02/12/HBuRsI.md.png)](https://imgtu.com/i/HBuRsI)
 　　[![HBKNtS.md.png](https://s4.ax1x.com/2022/02/12/HBKNtS.md.png)](https://imgtu.com/i/HBKNtS)
 
 8. 分析结果就是VMnet出现了问题，查看防火墙等配置，最后重启VMnet8网络后正常通信了.我的电脑每次重启或者熄屏后都要重新启VMnet8网络才能正常．
 　　[![HBM334.png](https://s4.ax1x.com/2022/02/12/HBM334.png)](https://imgtu.com/i/HBM334)
   
   > >>> 作者：吴强
