+++

author = "AzhiTakatoo"

title = "wyu_Linux大作业（Azhi"

date = "2020-12-25"

description = "磁盘清理工具脚本"

tags = ["markdown", "wyuCourse"]

categories = ["personal"]

images = ["img/2020/12/Azhi.jpg"]

aliases = ["migrate-from-jekyl"]

+++

<!-- ---

author: AzhiTakatoo

title: 磁盘清理工具

date: 2020-12-25

categories: ['大学课程']

draft: false

--- -->

<!--more-->

***

Linux系统-容振邦老师-大作业设计-磁盘清理工具

***

系统设计：

在系统中，首先程序根据用户输入的文件目录名与预留空间，读取到要清理的文件和判断磁盘的空间是否足够。如果磁盘空间大于预留空间，则不需要进行磁盘清理，程序停止；反之，则执行程序清理步骤。例如，现有目录text下文件test1.rar、test2.rar、test3.rar，其中test1.rar很久未被访问（未被访问时间：test3.rar<test2.rar<test1.rar），程序执行时，用户输入的要清理的文件目录text，程序会找出text目录下最长时间没有被访问的文件——test1.rar，接着在命令行中提示用户是否删除此文件。若用户输入“no”，则将文件test1.rar调出当前检索序列；若用户输入“yes”，则将此文件test1.rar移除至回收站，在此过程，用户所有操作都将记录在日志文件中；若输入错误，则系统会提示出错，请用户重新输入。之后程序会重复查找最长时间未被访问的文件的步骤，找到文件test2.rar，如此直至text目录下文件都被检索一遍或腾出足够的空间，程序结束。

基本流程图：

![cleanup流程图](https://tva1.sinaimg.cn/large/0081Kckwgy1gm00u0aifgj30pu0r9jsz.jpg)

代码如下：

```shell
#!/bin/sh

read -p "请输入需要清理的文件名称：" DirName
read -p "请输入预留的空间大小（kb单位）" FreeSpaceThreshold
DirCleanupList=(
		"$DirName"
	)

echo $FreeSpaceDT

FoundFiles=0
NoChangeCounter=0

arrayLen=${#DirCleanupList[*]}

while true; do
  FreeSpace=$(df /dev/sda1 --output=avail|tail -n 1)
  FreeSpaceDT=$((FreeSpace - FreeSpaceThreshold))
  echo "磁盘剩余空间：${FreeSpace}"
  echo "需要腾出空间：${FreeSpaceThreshold}" 
  if [ $FreeSpaceDT -lt 0 ]; then
    echo "磁盘空间不足，需要对磁盘文件进行清理。"
    for sDir in ${DirCleanupList[@]}; do
     OldestFile=${sDir}/$(ls -t1 --file-type ${sDir}|grep -v './$'|tail -n 1)

      #echo ${OldestFile} 
      if [ "$OldestFile" != "$sDir/" ]
      then
	(( FoundFiles += 1 ))
	echo "找到" ${FoundFiles} "个文件需要清理。"
	read -p "最长时间没有访问的文件是${OldestFile},请问是否确定删除？(yes/no)" sel
	if [ $sel = "yes" ]; then
	   echo "成功清除${OldestFile}"
	  mv ${OldestFile} ///dev/shm/recycle/
	  echo "[$(date)]  将${OldestFile}从${DirName}移除至回收站recycle" >> ///cleanLog.txt

	  if [ $(cat ///cleanLog.txt | wc -l) -ge 6 ]; then
	  sed -i '1d' ///cleanLog.txt
	  fi

	elif [ $sel = "no" ]; then
	  sed -i '1a AAAA' ${OldestFile}
	  sed -i '2d' ${OldestFile}
	  echo "将${OldestFile}剔除在检索列表外，进入下一轮文件检索。"
	  echo "[$(date)]  将${OldestFile}从当轮检索列表移除。" >> ///cleanLog.txt

	  if [ $(cat ///cleanLog.txt | wc -l) -ge 6 ]; then
          sed -i '1d' ///cleanLog.txt
          fi
	else
	  echo "输入错误，请重新输入。"
	  continue 1
        fi
       else
	echo "磁盘空间足够。"
      fi
      
    done
  else
    break
  fi

  FreeSpaceAfter=$(df /dev/sda1 --output=avail|tail -n 1)
  
  if [ "$FoundFiles" -eq 0 ]; then
    echo "没有更多文件需要清除了。"
    break
  fi
  
  FoundFiles=0
  
  #Test the freespace before & after if no change, increment the NoChangeCount - this is to avoid being stuck in an infinute loop
  if [ $FreeSpace -eq $FreeSpaceAfter ]; then
    (( NoChangeCounter += 1 ))
  else
    NoChangeCount=0
  fi
  
  #If there were no changes in the last 1000 loops, exit
  if [ $NoChangeCounter -gt 1000 ]; then
    echo "Exiting due to no change in Free Space"
    break;
  fi
  
done
```

