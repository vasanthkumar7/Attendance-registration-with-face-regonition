# Attendance-registration-with-face-regonition
 
# Introduction
Attendance registration used schools and colleges to check whether everyone is present and it is done manually . In this blog we gonna see about how to create a attendance registration application with face recognition  using python

# Abstract
To create a attendance registration application using python

# Headers

```
from tkinter import *
from cryptography.fernet import Fernet
import cv2
import numpy as np
import face_recognition
from tkinter import messagebox
import os
``` 
tkinter is used for graphical user interface
cryptography is used for encryption and decryption
cv2 is to get information from camera
numpy is used for arrays
face_recognition is used for detect face and get information about the face
os is used to delete files

# Starters

```
root=Tk()
root.title("Attendence registration")
``` 

# Functions

```
def remove_info():
    os.remove("present.txt")
    f=open("present.txt","a")
    f.close

def list_tostring(a):
    l=""
    for i in a:
        l+=str(i)+" "
    return l

def save_faces():
    
    messagebox.showinfo("Attendence", "Press 's' to capture ")
    cap = cv2.VideoCapture(0)
    while True:
        img = cap.read()
        img = np.array(img[1], dtype=np.uint8)
        cv2.imshow('img', img)
        if cv2.waitKey(1)& 0xFF == ord('s'):
            cv2.imwrite('test1.jpg',img)
            break
    cap.release()
    cv2.destroyAllWindows()
    

def save_done():
    global rollno,name,savestudent,cancel,frame1,frame2
    if rollno.get()=="" or name.get()=="":
        messagebox.showinfo("Attendence registration", "Enter your name and rollno")
        return
    known_image = face_recognition.load_image_file("test1.jpg")
    biden_encoding = face_recognition.face_encodings(known_image)[0]
    ass=list_tostring(list(biden_encoding))
    biden_encoding=list(map(float,ass.split()))
    biden_encoding=np.array(biden_encoding)
    f=open("students.txt","a")
    key = Fernet.generate_key()
    fernet = Fernet(key)
    encMessage = fernet.encrypt(ass.encode())
    enname=name.get()
    enrollno=rollno.get()
    encMessage1= fernet.encrypt(enname.encode())
    encMessage2 = fernet.encrypt(enrollno.encode())
    f.write(str(key)+" "+str(encMessage)+" "+str(encMessage1) +" "+str(encMessage2)+" \n")
    frame2.grid_forget()
    frame1.grid(row=0,column=0)

    


def check_facematch():
    messagebox.showinfo("Attendence", "Press 's' to capture ")
    flag=0
    cap = cv2.VideoCapture(0)
    while True:
        img = cap.read()
        img = np.array(img[1], dtype=np.uint8)
        cv2.imshow('img', img)
        if cv2.waitKey(1)& 0xFF == ord('s'):
            cv2.imwrite('test1.jpg',img)
            break
    cap.release()
    cv2.destroyAllWindows() 
    f=open("students.txt","r")
    ff=f.read().split("\n")
    for i in ff:
        j=i.split()
        if j==[]:
            break
        jkl=bytes(j[0][2:len(j[0])-1],'utf-8')
        fernet = Fernet(jkl)
        decMessage = fernet.decrypt(bytes(j[1][2:len(j[1])-1],'utf-8')).decode()
        decMessage2 = fernet.decrypt(bytes(j[2][2:len(j[2])-1],'utf-8')).decode()
        decMessage3=fernet.decrypt(bytes(j[3][2:len(j[3])-1],'utf-8')).decode()
        bdg=list(map(float,decMessage.split()))
        bdg=np.array(bdg)
        unknown_image = face_recognition.load_image_file("test1.jpg")
        try:
            unknown_encoding = face_recognition.face_encodings(unknown_image)[0]
        except:
            messagebox.showerror("Attendence", "Face is not detected")
            return 

        results = face_recognition.compare_faces([bdg], unknown_encoding)
        if results[0]:
            fopen=open("present.txt","r")
            check_given=1
            for k in fopen.read().split("\n"):
                try:
                    if k[0]==decMessage2:
                        check_given=0
                        messagebox.showinfo("Attendence", "Your have already given attendence ")
                except:
                    break
            fopen.close()
            fopen=open("present.txt","a")
            if check_given:
                fopen.write(decMessage2+" "+decMessage3+" \n")
                messagebox.showinfo("Attendence", "Your Attendence registered succesfully")
            fopen.close()


            flag=1
    if flag==0:
        messagebox.showinfo("Attendence", "Your profile doesn't exist")


def register_a_student():
    global frame1,frame2
    frame1.grid_forget()
    save_faces()
    frame2.grid(row=0,column=1)

def go_back():
    global frame1,frame2
    frame2.grid_forget()
    frame1.grid(row=0,column=1)


``` 

remove_info function will delete the attendance information

list_tostring function will convert list to string

save_faces function will take picture and save it as test1.png

save_done function will take name , roll number and face information and encrypt it with a cryptography key and save it in a students.txt file

check_facematch function will mark the attendance using facial information

register_a_student function will navigate to student siginup page

go_back function will navigate back to the strating page

# Rest of the code


```
#starting_page

frame1=Frame(root)


mark_my_attendence=Button(frame1, text ="Mark my attendence",command=check_facematch ,font=("Comic Sans MS", 20, "bold"),fg="white",bg="black")
mark_my_attendence.grid(row=0,column=0,padx=10,pady=10)
register=Button(frame1, text ="Register new student", command=register_a_student,font=("Comic Sans MS", 20, "bold"),fg="white",bg="black")
register.grid(row=1,column=0,padx=10,pady=10)
clear=Button(frame1, text ="Clear all attendence", command=remove_info,font=("Comic Sans MS", 20, "bold"),fg="white",bg="black")
clear.grid(row=2,column=0,padx=10,pady=10)





#register page

frame2=Frame(root)

text1=Label(frame2,text="Roll No:",font=('calibre',15,'normal')).grid(row=0,column=0)
rollno=Entry(frame2, font=('calibre',15,'normal'),width=25)
rollno.grid(row=1,column=0,pady=10,padx=10)

text1=Label(frame2,text="Name:",font=('calibre',15,'normal')).grid(row=2,column=0)
name=Entry(frame2, font=('calibre',15,'normal'),width=25)
name.grid(row=3,column=0,pady=10,padx=10)

savestudent=Button(frame2, text ="Save", command=save_done ,font=('calibre', 15, "bold"))
savestudent.grid(row=4,column=0,pady=10)

cancel=Button(frame2, text ="Cancel",command=go_back,font=( 'calibre',15, "bold"))
cancel.grid(row=5,column=0,pady=10)

frame1.grid(row=0,column=0)

root.mainloop()



``` 


![Screenshot (2).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1650376021405/1UISUMGOQ.png)


![Screenshot (3).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1650376038327/WYdyE0EDj.png)


![Screenshot (5).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1650376052516/pLsMvOo1A.png)


![Screenshot (6).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1650376067773/KgLFan36n.png)
