# Data_Strcture_and_Algorithm

#include <iostream>
#include <math.h>
#include <assert.h>
using namespace std;

// simple calculator
//Stack abstract class
template <typename E> class Stack {
private:
	void operator =(const Stack&){}
	Stack(const Stack&){}
public:
	Stack(){}
	virtual ~Stack(){}
	virtual void clear() = 0;
	virtual void push(const E& it) = 0;
	virtual E pop() = 0;
	virtual const E& topValue() const = 0;
	virtual int length()const = 0;
};
//Array-based stack implementation
template <typename E> class AStack :public Stack<E> {
private:
	int maxSize;
	int top;
	E *listArray;
public:
	AStack(int size = 100) {
		maxSize = size; top = 0; listArray = new E[size];
	}
	~AStack() { delete[] listArray; }
	void clear() { top = 0; }
	void push(const E& it) {
		assert(top != maxSize); "Stack is full";
		listArray[top++] = it;
	}
	E pop() {
		assert(top != 0); "Stack is empty";
		return listArray[--top];
	}
	const E& topValue()const {
		assert(top != 0); "Stack is empty";
		return listArray[top - 1];
	}
	int length() const { return top; }
};
int isp(char ch)	//获取并返回ch的栈内优先级
{
	if (ch == '=') return 0;
	else if (ch == '+') return 3;
	else if (ch == '-') return 3;
	else if (ch == '*') return 5;
	else if (ch == '/') return 5;
	else if (ch == '%') return 5;
	else if (ch == '(') return 1;
	else if (ch == ')') return 14;
	else if (ch == '^') return 7;
	else if (ch == '&') return 7;
	else if (ch == 'l') return 9;
	else if (ch == 's' || ch == 'c' || ch == 't') return 11;
	else if (ch == '!') return 13;
}

int osp(char ch)	//获取并返回ch的栈外优先级
{
	if (ch == '=') return 0;
	else if (ch == '+') return 2;
	else if (ch == '-') return 2;
	else if (ch == '*') return 4;
	else if (ch == '/') return 4;
	else if (ch == '%') return 4;
	else if (ch == '(') return 14;
	else if (ch == ')') return 1;
	else if (ch == '^') return 6;
	else if (ch == '&') return 6;
	else if (ch == 'l') return 8;
	else if (ch == 's' || ch == 'c' || ch == 't') return 10;
	else if (ch == '!') return 12;
}

int factorial(int x)
{
	if (x == 0) return 1;
	else return x * factorial(x - 1);
}

bool cal(char op, double x, double y, double &r)	// 计算r = x op y，计算成功，返回true
{
	if (op == '+')
	{
		r = x + y;
		return true;
	}
	else if (op == '-')
	{
		r = x - y;
		return true;
	}
	else if (op == '*')
	{
		r = x * y;
		return true;
	}
	else if (op == '/')
	{
		r = x / y;
		return true;
	}
	else if (op == '^')
	{
		r = pow(x, y);
		return true;
	}
	else if (op == '&')
	{
		r = pow(x, 1.0 / y);
		return true;
	}
	else if (op == '%')
	{
		int ix = (int)(x + 0.5);
		int iy = (int)(y + 0.5);
		r = ix % iy;
		return true;
	}
	else if (op == '|')
	{
		r = log(y) / log(x);
		return true;
	}
	else if (op == 's')
	{
		r = x * sin(y);
		return true;
	}
	else if (op == 'c')
	{
		r = x * cos(y);
		return true;
	}
	else if (op == 't')
	{
		r = x * tan(y);
		return true;
	}
	else if (op == '!')
	{
		int ix = (int)(x + 0.5);
		r = factorial(ix) * y;
		return true;
	}
	else return false;
}

void GetNextchar(char &ch)	//从输入的表达式中获取一个字符
{
	ch = cin.get();
}

bool isdigit(char ch)	//判断ch是否为数字0-9
{
	if (ch >= '0' && ch <= '9') return true;
	else return false;
}

bool IsOperator(char ch)	//判断ch是否为操作符
{
	if (ch == '+' || ch == '-' || ch == '*' || ch == '/' || ch == '^' || ch == '&' || ch == '%' || ch == '(' || ch == ')' || ch == '=' || ch == '|' || ch == 's' || ch == 'c' || ch == 't' || ch == '!') return true;
	else return false;
}

template <typename E>
bool Get2Operands(AStack<E> & opnd, double &x, double &y)	//从操作数栈中取2个操作数
{
	if (opnd.length() < 2) return false;
	x = opnd.pop();
	y = opnd.pop();
	return true;
}

int main()
{
	//生成一个即科学又帅比的界面
	cout << "(☆▽☆) (*^_^*) (☆▽☆) (*^_^*) (☆▽☆) (*^_^*) (☆▽☆) (*^_^*) (☆▽☆) (*^_^*) (☆▽☆) (*^_^*)" << endl;
	cout << endl;
	cout << "                                         Very NEW B Calculator" << endl;
	cout << "                                                        Designed by 侯牧村 吴盈 李宬睿 from group 666" << endl;
	cout << endl;
	cout << "Instructions:" << endl;
	cout << "1. This Very NEW B Calculator is able to calculte the expression that contains '+'(plus), '-'(minus), " << endl;
	cout << "   '*'(times), '/'(division), '^'(power), '&'(extract), '%'(remainder), '|'(log), 's'(sin), 'c'(cos), " << endl;
	cout << "   't'(tan) and '!'(factorial)." << endl;
	cout << "2. You can also use parentheses '(' and ')' to set the order." << endl;
	cout << "3. Decimals are supported in this Very NEW B Calculator." << endl;
	cout << "4. If you want to calculate log m (n), please input 'm|n.'" << endl;
	cout << "5. If the expression contains trigonometric functions, they will be calculate first, but you'd better" << endl;
	cout << "   use parentheses to set the order." << endl;
	cout << "6. The factorial can only accept non-negative integers." << endl;
	cout << endl;
	cout << "Please start your show~   *_* " << endl;
	cout << "#####################################################################################################" << endl;

	while (1)
	{
		cout << "Please enter the expression(The expression should end with '=','N' if you want to quit!):" << endl;
		AStack<double> OPND;	//操作数栈的定义
		AStack<char> OPTR;	//操作符栈的定义

		OPTR.push('=');
		char pre_char = '=';	//pre_char 表示当前处理字符的前一个字符，如为数，则其值'0'
		char ch;
		GetNextchar(ch);
		char OPTR_top = OPTR.topValue();
		while (OPTR_top != '=' || ch != '=')
		{
			if (ch == 'N') { cout << "Thanks for using!      *_*" << endl; return 0; }
			else if (isdigit(ch) || ch == '.')
			{
				double operand;
				cin.putback(ch);
				cin >> operand;
				OPND.push(operand);
				pre_char = '0';
				GetNextchar(ch);
			}
			else if (!IsOperator(ch))
			{
				cout << "表达式中出现非法字符！" << endl; return 0;
			}
			else
			{
				if ((pre_char == '=' || pre_char == '(') && (ch == '+' || ch == '-')) OPND.push(0);
				if (ch == 's' || ch == 'c' || ch == 't') OPND.push(1);
				if (pre_char == '!')
				{
					OPND.push(1);
					pre_char = '0';
				}
				if (isp(OPTR_top) < osp(ch))
				{
					OPTR.push(ch);
					pre_char = ch;
					GetNextchar(ch);
					OPTR_top = OPTR.topValue();
				}
				else if (isp(OPTR_top) > osp(ch))
				{
					double x, y, r;
					Get2Operands(OPND, x, y);
					char op = OPTR.pop();
					cal(op, y, x, r);
					OPND.push(r);
					OPTR_top = OPTR.topValue();
				}
				else
				{
					OPTR.pop();
					pre_char = ch;
					GetNextchar(ch);
					OPTR_top = OPTR.topValue();
				}
			}
			if (!OPTR_top) { cout << "表达式有错！" << endl; return 0; }
		}
		if (OPND.length() != 1) { cout << "表达式有错！" << endl; return 0; }
		double result = OPND.pop();
		cout << "The result is: " << result << endl;
		GetNextchar(ch);
	}
}
