# Python program to shift time 
# from a subtitle file. 

# Importing module required for regular 
# expressions

import re

t=1500
#t=int(input("Enter time to be shifted:"))
#user entered time in milliseconds to shift the subtitles
#sample : t=1512 indicates shift of 1 sec & 0.512 millisec
#to the subtitle file
#original (00:00:31,603 --> 00:00:34,939)
#to (00:00:33,115 --> 00:00:36,451)


def encrypt_time(h,m,s,ms):
    #1hour=3600000 milliseconds , 1minute=60000 milliseconds , 1second=1000 milliseconds, millisecond
    int_millitime=h*3600000+m*60000+s*1000+ms
    return int_millitime

def decrypt_time(int_millitime):
    #1hour=3600000 milliseconds , 1minute=60000 milliseconds , 1second=1000 milliseconds, millisecond
    h=int_millitime//3600000 #integer division
    #rem_time stands for remaining time
    rem_time=int_millitime-h*3600000
    m=rem_time//60000
    rem_time=rem_time-m*60000
    s=rem_time//1000
    rem_time=rem_time-s*1000
    ms=rem_time
    tuple1=(h,m,s,ms)
    return list(tuple1)


def time_shift(string,time_shift):
    #expected input type "00:00:31,603" in string
    #integer value in time_shift
    lst = re.findall('\d+', string)
    #lst = re.findall('\d\d(\d)?', string) works fine too maybe??
    #or lst=[]
    #lst[0]=string[0:2] and so on would work
    int_millitime=encrypt_time(int(lst[0]),int(lst[1]),int(lst[2]),int(lst[3]))
    new_millitime=int_millitime+time_shift
    new_list=decrypt_time(new_millitime)
    
    #set hh
    if(new_list[0]>=10):
        new_string=str(new_list[0])
    else:
        new_string=str(0)+str(new_list[0])

    new_string+=":"
    #set mm
    if(new_list[1]>=10):
        new_string+=str(new_list[1])
    else:
        new_string+=str(0)+str(new_list[1])
    new_string+=":"
    #set ss
    if(new_list[2]>=10):
        new_string+=str(new_list[2])
    else:
        new_string+=str(0)+str(new_list[2])
    new_string+=","
    
    #set msmsms
    if(new_list[3]>=100):
        new_string+=str(new_list[3])
    elif(new_list[3]>=10):
        new_string+=str(0)+str(new_list[3])
    else:
        new_string+=2*str(0)+str(new_list[3])
    
    
    return new_string

t=1512
#user entered time in milliseconds to shift the subtitles
#sample : t=1512 indicates shift of 1 sec & 0.512 millisec
#to the subtitle file
#original (00:00:31,603 --> 00:00:34,939)
#to (00:00:33,115 --> 00:00:36,451)

f=open(r"C:\Users\Farhan\AppData\Local\Programs\Python\Python37-32\subtitlefile.srt",'r')
#source file
q=open(r"C:\Users\Farhan\AppData\Local\Programs\Python\Python37-32\subtitlefilemodified.srt","w+")
#destination file
contents=f.readlines()
#reads each line in source
#sample'00:00:31,603 --> 00:00:34,939\n'
count=0
for line in contents:
    matchObj=re.match(r'(\d{2}:\d{2}:\d{2},\d{3})\s+-->\s+(\d{2}:\d{2}:\d{2},\d{3})',line,re.M|re.I)
    if matchObj:
        count+=1
        start=matchObj.group(1)
        end=matchObj.group(2)
        new_start=time_shift(start,t)
        new_end=time_shift(end,t)
        new_line=new_start+" -->  "+new_end+"\n"
        q.write(new_line)
    else:
        q.write(line)
print(count)
f.close()
q.close()
