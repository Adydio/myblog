---
title: C语言计算器
tags:
- 数据结构
- C语言
categories: 一些代码
---
# 基于栈的计算器（C语言）

> 作者：Dongzhuo Chen

```c
#include<stdio.h>
#include<stdlib.h> 
#include<assert.h>

#define MAXSIZE 100

char valid_op[] = "+-*/";//定义合法的运算符数组，方便调用相应运算功能
char opStack[MAXSIZE];//定义运算符栈，用于存放暂时不用的运算符 
int opTop = -1;
double numStack[MAXSIZE];//定义数栈，用于存放暂时不用的数 
int numTop = -1;

void push_op(char op);
char pop_op();
void push_num(double num);
double pop_num();

int read();//定义读入函数 
double num_temp;//暂时存放读到的数字 
char op_temp;//暂时存放独到的运算符 
int is_num, is_op;

//定义函数指针，调用相应运算功能
double add(double x, double y);
double sub(double x, double y);
double mul(double x, double y);
double divid(double x, double y);
double (*func[])(double x, double y) = {add, sub, mul, divid};

//定义优先级函数，用于判断左侧运算符与右侧运算符优先级 
int prio_l(char op); 
int prio_r(char op); 

//定义储存表达式的数组 
char s[MAXSIZE];
//定义pt变量，用于指示目前表达式读取进度 
int pt = 0;
double num1, num2;
char op0;

int main()
{
	int i, op_loc;
	printf("请输入一个表达式(目前仅支持正数的加减乘除,可以使用括号)，以#开头，并以#结束：\n");
	scanf("%s", s);
	read();
	push_op(op_temp);//读到第一个“#”，并将其存入运算符栈 
	read();
	while(true)
	{
		if(is_num == 1)//如果读到的是数字，将其存入数栈 
		{
			push_num(num_temp);
		}
		if(is_op == 1)//如果读到的是运算符，判断其与运算符栈顶符号的优先级 
		{
			if(prio_l(opStack[opTop]) < prio_r(op_temp))//若读到的运算符优先级较高，将其存入运算符栈 
			{
				push_op(op_temp);
			}
			else if(prio_l(opStack[opTop]) == prio_r(op_temp))//若读到的运算符优先级相等，说明左右括号相遇，去括号 
			{
				pop_op();
			}
			else if(prio_l(opStack[opTop]) > prio_r(op_temp))//若读到的运算符优先级较低，则先进行运算符栈顶的运算符运算 
			{
				num2 = pop_num();
				num1 = pop_num();
				op0 = pop_op();
				for(i = 0; i < 4; i ++)
				{
					if(op0 == valid_op[i])
					{
						op_loc = i;
						break;
					}
				}
				num_temp = (*func[op_loc])(num1,num2);
				push_num(num_temp);//将得到的结果存入数栈中 
				pt --;  
			}
		}
		if(is_num==0 && op_temp=='#' && opStack[opTop]=='#')//两个#相遇时标志着运算结束 
		{
			break;
		} 
		read();//读取下一个字符 
	}
	printf("这个表达式的结果是%f\n", numStack[numTop]);
	system("pause");
	return 0; 
}

int read()
{
	if(s[pt] >= '0' && s[pt] <= '9')//如果读到的是数字 ，把读到的数字字符转换成对应的数值 
	{
		is_num = 1;
		is_op = 0;
		double a = 0;
		int t = 10;
		while(s[pt] >= '0' && s[pt] <= '9')//处理小数点以前的位数
		{
			a = 10 * a + s[pt] - '0';
			pt ++;
		}
		if(s[pt] == '.')
		{
			pt ++;
		}
		while(s[pt] >= '0' && s[pt] <= '9')//处理小数点以后的位数 
		{
		a = a + (s[pt] - '0')*1.0/t;
		pt ++;
		t *= 10;
		}
		num_temp = a;
		return 0;		
	}
	else//如果读到的是运算符 
	{
		is_op = 1;
		is_num = 0;
		op_temp = s[pt];
		pt ++;
		return 0;
	}
}
void push_op(char op)
{
	if(opTop == MAXSIZE-1)
	{
		printf("operator stack is full!\n");
		assert(0);
	}
	else
	{	
		opTop ++;
		opStack[opTop]=op;	
	}
}

char pop_op()
{
	char op;
	if(opTop == -1)
	{
		printf("operator stack is empty!\n");
		assert(0);
	}
	else
	{	
		op = opStack[opTop];
		opTop --;
	}
	return op;
}

void push_num(double num)
{
	if(numTop == MAXSIZE)
	{
		printf("number stack is full!\n");
		assert(0);
	}
	else
	{
		numTop ++;
		numStack[numTop]=num;
	}
}

double pop_num()
{
	double num;
	if(numTop == -1)
	{
		printf("number stack is empty!\n");
		assert(0);
	}
	else
	{
		num = numStack[numTop];
		numTop --;
	}
	return num;
} 

int prio_l(char op)
{
	int prio;
	switch(op)
	{
		case'+':
		case'-':
			prio = 4;
			break;
		case'#':
			prio = 0;
			break;
		case'*':
		case'/':
			prio = 6;
			break;
		case'(':
			prio = 2;
			break;
		case')':
			prio = 7;
			break;
	}
	return prio;
}
int prio_r(char op)
{
	int prio;
	switch(op)
	{
		case'+':
		case'-':
			prio = 3;
			break;
		case'#':
			prio = 0;
			break;
		case'*':
		case'/':
			prio = 5;
			break;
		case'(':
			prio = 7;
			break;
		case')':
			prio = 2;
			break;
	}
	return prio;
}

double add(double x, double y)
{
	double ret = 0;
	ret = x + y;
	return ret;
}

double sub(double x, double y)
{
	double ret = 0;
	ret= x - y;
	return ret;
 } 
 
double mul(double x, double y)
{
	double ret = 0;
	ret = x * y;
	return ret;
}

double divid(double x, double y)
{
	double ret = 0;
	ret= x / y; 
	return ret; 
}

```
