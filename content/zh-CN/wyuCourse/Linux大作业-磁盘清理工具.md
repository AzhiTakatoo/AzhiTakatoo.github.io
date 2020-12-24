+++

author = "AzhiTakatoo"

title = "途经人间"

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

代码如下：

```shell
#!/bin/bash

DirCleanupList=('/var/motion/cam1'
		'/var/motion/cam2'
		'/var/motion/cam3'
	)
:<<EOF
This command generates a sorted directory listing - Oldest file first - Only files.   The grep command at the end takes out the directories.
 ls -t1r --file-type /var/motion/cam1|grep -v './$'
 To get only the single oldest file
 ls -t1r --file-type /var/motion/cam1|grep -v './$'|head -n 1

 This gets the available space on the drive
 df /dev/sda1 --output=avail|tail -n 1
EOF



#FreeSpaceThreshold determines when to start removing the oldest files in each directory

#空余空间极限值
FreeSpaceThreshold=721000

#echo $FreeSpaceDT

FoundFiles=0
NoChangeCounter=0

while true; do
#df显示指定磁盘文件的可用空间。如果没有文件名被指定，则所有当前被挂载的文件系统的可用空间将被显示。
#将被挂载的文件sda1输出可用空间，并作为tail的输入传给tail，输出sda1倒数第一行（管道符号）
  FreeSpace=$(df /dev/sda1 --output=avail|tail -n 1)
  FreeSpaceDT=$((FreeSpace - FreeSpaceThreshold))

  if [ $FreeSpaceDT -lt 0 ]; then
    for sDir in ${DirCleanupList[@]}; do
      echo ${sDir}
      
      #2比1大的情况下，下面判断为真
      OldestFile=${sDir}/$(ls -t1r --file-type ${sDir}|grep -v './$'|head -n 1)
      
      if [ "$OldestFile" != "$sDir/" ]
      then
	(( FoundFiles += 1 ))

	echo "Removing ${OldestFile}"
	rm -f ${OldestFile}
      fi
    
    
    done
  else
    break
  fi
  
  echo "Found" ${FoundFiles}

  FreeSpaceAfter=$(df /dev/sda1 --output=avail|tail -n 1)
  
  #Exit if no files were found to clean up
  #判断条件为？
  if [ "$FoundFiles" -eq 0 ]; then
    echo "No more files found to clean"
    break
  fi
  
  FoundFiles=0
  
  #Test the freespace before & after if no change, increment the NoChangeCount - this is to avoid being stuck in an infinute loop
  if [ $FreeSpace -eq $FreeSpaceAfter ]; then
    (( NoChangeCounter += 1 ))
  else
  #
    NoChangeCount=0
  fi
  
  #If there were no changes in the last 1000 loops, exit
  if [ $NoChangeCounter -gt 1000 ]; then
    echo "Exiting due to no change in Free Space"
    break;
  fi
  
done
```

