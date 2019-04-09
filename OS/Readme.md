Question:

There are 3 student processes and 1 teacher process. Students are supposed to do their assignments and they need 3 things for that- pen,paper and question paper. The teacher has an infinite supply of all the three things. One student has pen, another has paper and another has question paper. The teacher places two things on a shared table and the student having the third complementary thing makes the assignment and tells the teacher on completion. The teacher then places two things out of three and again the student having the third thing makes the assignment and tells the teacher on completion. This cycle continues. Write a program to synchronise the teacher and the students.
 * Two types of people can enter into a library-students and teachers. After entering the library, the visitor searches for the required books and get them. In ordr to get them issued, he goes to the single CPU which is there to process the issuing of books. Two types of queues are there at the counter-one for students and one for teachers.A student goes and stands at the tail of the queue for students and simliarly the teacher goes and stands at the tail of the queue for teaches (FIFO).If a student is being served and a teacher arrives at the counter, he would be the next person to get service (PRIORITY - non preemtive). If two teachers arrive at the same time, they will stand in their queue to get service (FIFO). Write a program to ensure that the system works in a non-chaotic manner.
 * If a teacher is being served and during the period when he is being served, another teacher comes, then that teacher would get the service next. This process might continue leading to increase in waiting time of students. Ensure in your program that the waiting time of students is minimized.

Description:

>The question has two scenarios between students and teacher at Examination hall and Library
> I broke the question into two parts:- 
	
Scene 1:	
	There are 3 studentprocesses and 1 teaher process. Students are supposed to do their assignments and they need 3 things for that- pen,paper and question paper. The teacher has an infinite supply of all the three things. One student has pen, another has paper and another has question paper. The teacher places two things on a shared table and the student having the third complementary thing makes the assignment and tells the teacher on completion. The teacher then places two things out of three and again the student having the third thing makes the assignment and tells the teacher on completion. This cycle continues. Write a program to synchronise the teacher and the students.

Sample Input:

1

1
2
3

1
2

2
3

3
1

Expected Output:

******3rd Student has the last item, he completes the task and reports to teacher******


******1st Student has the last item, he completes the task and reports to teacher******


******2nd Student has the last item, he completes the task and reports to teacher******

Scene 1 at Exam Hall is complete

Explanation:
		Initially the program assigns the students with the resources they have (i.e., pen or paper or question paper ) as stated by question. Then program prompts for the resources to be placed on shared table by the teacher, according to the user input the variables will be allocated to the student who can complete assignment and then the student acknowledges the teacher about his completion. Threads and mutex are used here to determine a continous and uninterupted flow.

Scene 2:
	 * Two types of people can enter into a library-students and teachers. After entering the library, the visitor searches for the required books and get them. In ordr to get them issued, he goes to the single CPU which is there to process the issuing of books. Two types of queues are there at the counter-one for students and one for teachers.A student goes and stands at the tail of the queue for students and simliarly the teacher goes and stands at the tail of the queue for teaches (FIFO).If a student is being served and a teacher arrives at the counter, he would be the next person to get service (PRIORITY - non preemtive). If two teachers arrive at the same time, they will stand in their queue to get service (FIFO). Write a program to ensure that the system works in a non-chaotic manner.
 * If a teacher is being served and during the period when he is being served, another teacher comes, then that teacher would get the service next. This process might continue leading to increase in waiting time of students. Ensure in your program that the waiting time of students is minimized.

Sample Input:

2

3

1
1
0

2
1
0

3
1
0

Expected Output:

Person[1]:	Time:	1 To 2

Person[2]:	Time:	2 To 3

Person[3]:	Time:	3 To 4

Explanation:

The above problem can be solved by using multi level scheduling for both the teacher queue and the student queue. However starvation occurs when new teacher comes before the current student is addressed the student have to wait, if this continues then the student will have longer waiting time; so to minimise this, I have coded this using 
"Multilevel Feedback Queue Scheduling" which enables process switching during runtime. This incorporates both the first come first serve technique along with shortest job first. However we have the process time of issuing book of teacher and student same. The code minimises the student waiting time also maintains the priority allocated for both of them at the same time.

code:

```

#include<stdio.h>
#include<unistd.h>
#include<pthread.h>
#include<stdlib.h>
#include<stdio.h> 

#define N 10 

typedef struct 
{ 
      int person_id, arrival_time, burst_time, priority;
      int q, ready; 
}person_structure; 

int Queue(int t1) 
{ 
      if(t1 == 0 || t1 == 1 || t1 == 2 || t1 == 3) 
      { 
            return 1; 
      } 
      else
      {
            return 2; 
      }
} 

pthread_mutex_t lockt=PTHREAD_MUTEX_INITIALIZER;
int s0[3];
int s1[3];
int s2[3];
int assign(int x,int y)
{
int i=1;
if(y==0)
{
	if(s1[x]==1 || s2[x]==1)
	{
		printf("\nTwo students can't have same resource.");
		exit(0);
	}
}
if(y==1)
{
	if(s0[x]==1 || s2[x]==1)
	{
		printf("\nTwo students can't have same resource.");
		exit(0);
	}
}
if(y==2)
{
	if(s0[x]==1 || s1[x]==1)
	{
		printf("\nTwo students can't have same resource.");
		exit(0);
	}
}
return i;
}
void *student0()
{
	printf("\nPress 1 for pen, 2 for paper and 3 for question paper\n");
	printf("Enter the item present with 1st student: ");
	int a;
	pthread_mutex_lock(&lockt);	
	scanf("%d",&a);
	pthread_mutex_unlock(&lockt);
	if(a==1)
	{
		s0[0]=assign(0,0);
	}
	else{ if(a==2)
	{
		s0[1]=assign(1,0);
	}
	else{ if(a==3)
	{
		s0[2]=assign(2,0);
	}
	else
	{
		printf(" A student isn't configured to contain this.\n");
	}}}
}
void *student1()
{
	printf("\nPress 1 for pen, 2 for paper and 3 for question paper\n");
	printf("Enter the item present with 2nd student: ");
	int a;
	pthread_mutex_lock(&lockt);	
	scanf("%d",&a);
	pthread_mutex_unlock(&lockt);
	if(a==1)
	{
		s1[0]=assign(0,1);
	}
	else{ if(a==2)
	{
		s1[1]=assign(1,1);
	}
	else{ if(a==3)
	{
		s1[2]=assign(2,1);
	}
	else
	{
		printf(" A student isn't configured to contain this.\n");
	}}}
}
void *student2()
{
	printf("\nPress 1 for pen, 2 for paper and 3 for question paper\n");
	printf("Enter the item present with 3rd student: ");
	int a;
	pthread_mutex_lock(&lockt);	
	scanf("%d",&a);
	pthread_mutex_unlock(&lockt);
	if(a==1)
	{
		s2[0]=assign(0,2);
	}
	else{ if(a==2)
	{
		s2[1]=assign(1,2);
	}
	else{ if(a==3)
	{
		s2[2]=assign(2,2);
	}
	else
	{
		printf("A student isn't configured to contain this.\n");
	}}}
}
void *table()
{
	int i,a,b,c,d,e,f;
	e=0;f=0;d=0;
	for(i=0;i<3;i++)
	{
		printf("\n Now state what you want to place on the shared table ( 1.Pen  2.Paper  3.Question Paper ): ");
		printf("\n Enter first thing: ");
		pthread_mutex_lock(&lockt);	
		scanf("%d",&a);
		pthread_mutex_unlock(&lockt);	
		printf(" Enter second thing: ");
		pthread_mutex_lock(&lockt);	
		scanf("%d",&b);
		pthread_mutex_unlock(&lockt);
		if (a==1 && b==2 || a==2 && b==1)
		{ 										
			c=3;
		}
		else{ if (a==1 && b==3 || a==3 && b==1)
		{
			c=2;
		}
		else{ if (a==2 && b==3 || a==3 && b==2)
		{
			c=1;
		}
		else
		{
			printf("\nPlease check your input; Your input might be wrong or You have entered the same resources twice which will yield no result\n");
			exit(0);
		}}}

		if(s0[c-1]==1 && d==0)
		{	
		 	printf("******1st Student has the last item, he completes the task and reports to teacher******\n");
			d=1;
		}
		else{ if(s1[c-1]==1 && e==0)
		{
			printf("******2nd Student has the last item, he completes the task and reports to teacher******\n");
			e=1;
		}
		else{ if(s2[c-1]==1 && f==0)
		{
			printf("******3rd Student has the last item, he completes the task and reports to teacher******\n");
			f=1;
		}
		else { printf("\nStudent having the coreesponding last item has either completed his task\n");}}}
	}
}

void main()
{
	pthread_t Std0;
	pthread_t Std1;
	pthread_t Std2;
	pthread_t Tea1;
	int a;
	printf("There are 2 scenes in this simulation, Scene 1 : In Exam Hall & Scene 2 : In Library\n");
	printf("Enter choice: ");scanf("%d",&a);
switch(a)
{
case 1:
	printf("Scene Illustration: \nThere are 3 studentprocesses and 1 teaher process. Students are supposed to do their assignments and they need 3 things for that- pen,paper and question paper. The teacher has an infinite supply of all the three things. One student has pen, another has paper and another has question paper. The teacher places two things on a shared table and the student having the third complementary thing makes the assignment and tells the teacher on completion. The teacher then places two things out of three and again the student having the third thing makes the assignment and tells the teacher on completion. This cycle continues.\n\n");
	pthread_create(&Std0,NULL,student0,NULL);
	pthread_join(Std0,NULL);
	pthread_create(&Std1,NULL,student1,NULL);
	pthread_join(Std1,NULL);	
	pthread_create(&Std2,NULL,student2,NULL);
	pthread_join(Std2,NULL);
	pthread_create(&Tea1,NULL,table,NULL);
	pthread_join(Tea1,NULL);
	printf("\nScene 1 at Exam Hall is complete\n");
	break;exit(0);
case 2:
	{printf("Scene Illustration: \nTwo types of people can enter into a library-students and teachers. After entering the library, the visitor searches for the required books and get them. In ordr to get them issued, he goes to the single CPU which is there to process the issuing of books. Two types of queues are there at the counter-one for students and one for teachers.A student goes and stands at the tail of the queue for students and simliarly the teacher goes and stands at the tail of the queue for teaches (FIFO).If a student is being served and a teacher arrives at the counter, he would be the next person to get service (PRIORITY - non preemtive). If two teachers arrive at the same time, they will stand in their queue to get service (FIFO).\n If a teacher is being served and during the period when he is being served, another teacher comes, then that teacher would get the service next. This process might continue leading to increase in waiting time of students and the program times to minimise this.\n\n");
	int limit, count, temp_person, time, j, y; 
      person_structure temp; 
      printf("Enter Total Number of Persons:\t"); 
      scanf("%d", &limit);  
      person_structure person[limit]; 
      for(count = 0; count < limit; count++) 
      { 
            printf("\nPerson No (on basis of his arrival):\t"); 
            scanf("%d", &person[count].person_id); 
            printf("Arrival Time (on basis of his arrival with respect to his previous ):\t"); 
            scanf("%d", &person[count].arrival_time); 
	    person[count].burst_time=1; 
            printf("Person Priority (1 for Student and 0 for teacher):\t"); 
            scanf("%d", &person[count].priority); 
            temp_person = person[count].priority; 
            person[count].q = Queue(temp_person);
            person[count].ready = 0; 
      }
      time = person[0].burst_time; 
      for(y = 0; y < limit; y++) 
      { 
       for(count = y; count < limit; count++) 
            { 
                  if(person[count].arrival_time < time) 
                  {
                        person[count].ready = 1; 
                  } 
            } 
            for(count = y; count < limit - 1; count++) 
            {
            for(j = count + 1; j < limit; j++) 
                 { 
                        if(person[count].ready == 1 && person[j].ready == 1) 
                        { 
                              if(person[count].q == 2 && person[j].q == 1) 
                              { 
                                    temp = person[count]; 
                                    person[count] = person[j]; 
                                    person[j] = temp; 
                              } 
                        } 
                  } 
            } 
            for(count = y; count < limit - 1; count++) 
            { 
            for(j = count + 1; j < limit; j++) 
                  {
                        if(person[count].ready == 1 && person[j].ready == 1) 
                        { 
                              if(person[count].q == 1 && person[j].q == 1) 
                              { 
                                    if(person[count].burst_time > person[j].burst_time) 
                                    { 
                                          temp = person[count]; 
                                          person[count] = person[j]; 
                                          person[j] = temp; 
                                    } 
                                    else 
                                    { 
                                          break; 
                                    } 
                              } 
                        } 
                  } 
            } 
            printf("\nPerson[%d]:\tTime:\t%d To %d\n", person[y].person_id, time, time + person[y].burst_time); 
            time = time + person[y].burst_time; 
            for(count = y; count < limit; count++) 
            { 
                  if(person[count].ready == 1) 
                  { 
                        person[count].ready = 0; 
                  } 
            } 
      } } break;
default:
	printf("Scene doesn't match any of the listed, Have a Good Day!");
}
}


```

Output:

Scene 1:

******3rd Student has the last item, he completes the task and reports to teacher******


******1st Student has the last item, he completes the task and reports to teacher******


******2nd Student has the last item, he completes the task and reports to teacher******

Scene 1 at Exam Hall is complete

Scene 2:

Person[1]:	Time:	1 To 2

Person[2]:	Time:	2 To 3

Person[3]:	Time:	3 To 4

Scene 2 at Library is complete.


