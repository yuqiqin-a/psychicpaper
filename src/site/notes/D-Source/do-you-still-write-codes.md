---
{"dg-publish":true,"permalink":"/D-Source/do-you-still-write-codes/","created":"2024-02-28T10:19:58.395+08:00"}
---


# 当英语老师之后你还写代码吗？

## 前言

前不久有位从前司离职的教研找到我，想和我聊聊天。因为他之前对这份工作抱着非常大的热情，也曾渴望用自己的编程知识施展一番拳脚，但是奈何种种因素不得不选择离开。

当天我们聊得挺愉快，我把我在前司工作的所有经验和经历都悉数分享给他。

临走前他问我，“你当英语老师之后，现在还在写代码吗？”

我毫不犹豫地回答他，“还在的。”

于是这篇博客就想和大家分享一下，当了雅思老师之后我到底究竟用代码写了什么。

## 1. 统计课时

在我刚入职这家机构的时候，学校是没有教务的。

每个月老师上课的课时数是由教研主管统计的。这个统计工作指的是计算出每个老师这个月分别上了多少课时的一对一和班课，因为不同班型的课时费是不一样的。她甚至还需要制作工资条给老师们核对，并最终汇总给财务报税。

听起来很不可思议吧？教研主管要做这样的工作。

我和我的+1非常聊得来，关系也很好，我也非常乐意用我自己的专业知识帮助她减轻工作量。

在刚开始的时候，她需要在[教务宝](https://jwb.sc-edu.com/)上把每个老师的当月上课记录的excel下载下来，一个个点开计算课时。这显然可以用一个循环遍历所有的文件，自动计算。

![Pasted image 20240228111340.png](/img/user/B-Attachment/Pasted%20image%2020240228111340.png)

所以我的1.0版本脚本便是帮她完成这个工作。

但是由于我拿到的这个表格文件，只写了班级类型是班课还是一对一，并没有告诉我是几人班。所以我就得自己想办法提取人数。

唯一可靠的线索就是班级名称——如果是二人班，往往班级名字会被命名为「John&Marry」；如果是三人班，班级名字则是「John&Marry&Sam」。所以理论上我split一下班级名字，我就能够很可靠地得到班级人数了。

但是！由于之前的助教在创建班级时缺乏一个SOP，导致连接人名的字符千奇百怪，有中英文甚至emoji的加号，还有中英文的&，甚至是缩进tab和空格，于是我就必须得想办法做兜底，把所有情况都考虑进去。

```Python
import re

def get_separators(class_name: str) -> list:  
    pattern = r"[\+➕＋&＆ ]"    
    separators = re.findall(pattern, class_name)  
    return separators
```

虽然最后实测下来，还是会有10%左右的数据因为莫名其妙的原因统计不到。但是到这一步，其实就已经能够帮助减轻我的+1非常大的负担了。

后来我们又发现，其实教务宝支持导出all-in-one包含所有老师数据的大表格，这让统计的精确度又上升了不少。

![Pasted image 20240228112335.png](/img/user/B-Attachment/Pasted%20image%2020240228112335.png)

因为我只需要遍历一个文档就能完成统计，中间由于格式、数据等各种因素出现的误差概率被减少到了最小，出现不匹配的情况，我也只需要打开一个文档就能检查。

因此，在这个阶段的代码实战，再次验证了我在[[D-Source/from-shimo-to-excel-to-shimo\|这篇文章]]里说的观点——「宇宙万物的尽头就是Excel表格」，无论是能用好Excel宏还是Python，都将极大地提升文职的工作效率。

```Python
import pandas as pd
import argparse

def main():  
    input_path = args.input_path  
    output_path = args.output_path  
  
    df = pd.read_excel(input_path)  
    teacher_list = df["主讲老师"].unique()  
  
    for teacher in teacher_list:  
        teacher_data = df[df['主讲老师'] == teacher].reset_index(drop=True)  
        error_file_path = f"{output_path}/{teacher}_error.txt"  
        course_count = process_course_count(teacher_data, error_file_path)  
        course_count_value = sum(course_count.values())  
  
        # 检查总课时与计算数是否一致  
        sum_hours = sum(teacher_data['计主讲老师课时'])  
        if sum_hours != course_count_value:  
            with open(error_file_path, "a") as a:  
                a.write(f"教务宝总计{sum_hours}小时，自动计算{course_count_value}小时，相差{sum_hours-course_count_value}小时")  
        course_count["总课时"] = course_count_value  
        save_course_count_to_excel(course_count, f"{output_path}/{teacher}.xlsx")
```

为了方便我的+1操作，我甚至还请chat老师使用tkinter写了一个非常非常原始的GUI界面，虽然简陋，但是管用，非常适合不会使用Python的小白操作。

```Python
from tkinter import *  
from tkinter import filedialog  
from tkinter import messagebox  # Import messagebox module  
import subprocess

class Application(Frame):
	def run_script(self):  
	    script_path = self.script_text.get()  
	    input_path = self.input_path_text.get()  
	    output_path = self.output_path_text.get()  
	  
	    # Check if input and output paths have been selected  
	    if input_path == "Not Selected" or output_path == "Not Selected":  
	        messagebox.showerror("Error", "Please select both input and output paths.")  
	        return  
	  
	    # Call your script using subprocess with Python 3  
	    # script_path = "/Users/yuqiqin/Desktop/calculate_hours/calculate_salary.py"    command = f"python3 {script_path} --input_path {input_path} --output_path {output_path}"  
	    process = subprocess.Popen(command.split(), stdout=subprocess.PIPE, stderr=subprocess.PIPE)  
	    output, error = process.communicate()  
	  
	    # Check if there were any errors during script execution  
	    if process.returncode != 0:  
	        messagebox.showerror("Error", f"Error running script: {error.decode()}")  
	        return  
	  
	    messagebox.showinfo("Success", "计算完毕")
```

![Pasted image 20240228113011.png](/img/user/B-Attachment/Pasted%20image%2020240228113011.png)

## 2. 还是统计课时

时光一转，我亲爱的+1由于工作压力太大主动辞职了。

+2把这部分统计课时的工作交给了各位老师，希望大家自己完成每天的课时填写。

![Pasted image 20240228113621.png](/img/user/B-Attachment/Pasted%20image%2020240228113621.png)

但我才不想手动填写呢！

拿不到Excel表格我就只能自己想办法去教务宝的官方网站拿数据，在这里特别感谢Koni老师在我还在前司的时候教会了我调取API的技能。

```Python
def get_course_data(self, start_date, end_date):  
    url = "https://jwb.sc-edu.com/api/cal/list/"  
    payload = {  
        'branch_id': 12345,  
        'team_id': None,  
        'teacher_id': 0,  
        'mem_id': None,  
        'room_id': None,  
        'course_id': None,  
        'week': -1,  
        'start': start_date,  
        'end': end_date,  
        'page': 1,  
        'limit': 1500,  
        'over': -1,  
        'appoint': -1,  
        'cal_type': None,  
        'check_conflict': 1  
    }  
    data = requests.request(method="POST", url=url, headers=self.headers, data=payload).json()  
    return data
```

填好Headers和Payload之后，我拿到了我账号权限内能看到的所有数据，有些是在客户端UI查看不到的，甚至还看到了一些让你摸不着头脑「为什么这玩意不加密一下！？」的内容。

拿到了json数据就相当于拿到了一切！这个比Excel表格精确多了！

至此，我只需要修改一下发起请求的cookie，我也能帮我身边的小伙伴自动统计课时了。我赶紧把所有的函数都写成一个类。

我现在甚至还能算一些在客户端上不好算的数据。

比如在客户端上你只能看到自己上了多少个小时的课，现在我可以统计一下我2023年每个科目**各**上了多少个小时。我还可以看在过去的一年里，我带了多少个班级，带了多少个学生，有多少学生已经结课了……甚至直接生成一份H5风格的年终总结。

```Python
def main():  
    teacher = 'Alastair'  
    ts = TimetableSpy(teacher)  
    course_data = ts.get_course_data("2023-01-01", "2023-12-31")  
    data_file = f'data/data_2023.json'  
    ts.save_json(course_data['data']['lists'], data_file)  
  
    with open(data_file) as file:  
        data = json.load(file)  
        course_hours = ts.count_course_hours(data)  
        print(sum(course_hours.values()))  
  
        subject_hours = ts.count_subject_hours(data)  
        print(sorted(subject_hours.items(), key=lambda x: x[1], reverse=True))
```

```
1492
[('雅思听力', 578), ('基础语法', 329), ('雅思口语', 227), ('朗思口语', 108), ('Demo试听课', 100), ('PTE口语', 98), ('基础语音词汇', 31), ('达人班精听', 10), ('朗思听力', 4), ('达人班听力', 2), ('达人班精读', 1)]
```

## 后记

或许之后学校会招一个教务来接手算课时、算薪资的工作，但是掌握这样的核心技能就永远不会担心别人算错。所有的一切都是自动计算的，只需要点击1个按钮即可。

所以，我在当英语老师之后还在写代码吗？

是的，而且我每个月都能用上我写的代码。

我觉得我的编程能力能达到今天这样的程度，真的就是在一个又一个真实的需求和项目中操练出来的。非常感谢之前给予我帮助过的人，爱你们！

---
写于2024-02-28