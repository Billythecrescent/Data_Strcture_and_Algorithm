#include <iostream>
#include <assert.h>
#include <string>
using namespace std;

//VS环境下运行 
//第二次实验
//HuffNode的定义
template <typename E> class HuffNode {
public:
	virtual ~HuffNode() {}
	virtual int weight() = 0;
	virtual bool isLeaf() = 0;
};
//TreeNode的定义
template <typename E>
class TreeNode : public HuffNode<E> {
private:
	int wgt;//权重永远为int类型
	E it;
	TreeNode<E>* lc;
	TreeNode<E>* rc;
public:
	TreeNode(int weight,TreeNode<E>*l = NULL, TreeNode<E>*r = NULL) { wgt = weight; lc = l; rc = r; }
	~TreeNode() {}
	int weight() { return wgt; }
	E value() { 
		if (lc == NULL && rc == NULL) {
			return it;
		} 
		else { return NULL; } 
	}
	void setIt(E val) { it = val; }
	bool isLeaf() { return (lc == NULL) && (rc == NULL); }
	TreeNode<E>*left() { return lc; }
	void setLeft(TreeNode<E>*b) { lc = (TreeNode<E>*)b; }
	TreeNode<E>*right() { return rc; }
	void setRight(TreeNode<E>*b) { rc = (TreeNode<E>*)b; }
};

//Heap class
template <typename E>
void nswap(E A[], int i, int j) {
	E temp = A[i];
	A[i] = A[j];
	A[j] = temp;
}
template <typename E>
bool operator < (TreeNode<E>p1, TreeNode<E>p2) {  //重载操作符<，支持节点的比较
	return (p1.weight() < p2.weight());
}
template <typename E> class minheap {
private:
	E* Heap;
	int maxsize;
	int n;
	void siftdown(int pos) {
		while (!isLeaf(pos)) {
			int j = leftchild(pos);int rc = rightchild(pos);
			if ((rc < n) && (*Heap[rc]<*Heap[j])) j = rc;
			if (*Heap[pos]<*Heap[j])return;
			nswap(Heap, pos, j); pos = j;
		}
	}
public:
	minheap(E *h, int max, int num = 0) 
	{
		maxsize = max; n = num; Heap = h; buildHeap();
	}
	int size() const
	{
		return n;
	}
	bool isLeaf(int pos) const
	{
		return (pos >= n / 2) && (pos < n);
	}
	int leftchild(int pos) const
	{
		return 2 * pos + 1;
	}
	int rightchild(int pos) const
	{
		return 2 * pos + 2;
	}
	int parent(int pos) const
	{
		return (pos - 1) / 2;
	}
	void buildHeap()
	{
		for (int i = n / 2 - 1; i >= 0; i--) siftdown(i);
	}
	void insert(const E& it) {
		assert(n < maxsize); "Heap is full";
		int curr = n++;
		Heap[curr] = it;
		while ((curr != 0) && (*Heap[curr]<*Heap[parent(curr)]))
		{
			nswap(Heap, curr, parent(curr));
			curr = parent(curr);
		}
	}
	E removefirst() {
		assert(n > 0); "Heap is empty";
		nswap(Heap, 0, --n);
		if (n != 0) siftdown(0);
		return Heap[n];
	}
	E remove(int pos) {
		assert((pos >= 0) && (pos < n)); "Bad position";
		if (pos == (n - 1)) n--;
		else {
			nswap(Heap, pos, --n);
			while ((pos != 0) && (Heap[pos]<Heap[parent(pos)]))
			{
				nswap(Heap, pos, parent(pos));
				pos = parent(pos);
			}
			if (n != 0) siftdown(pos);
		}
		return Heap[n];
	}
};

//创立一个结构体Weight，里面存的是节点的频数和编码(初始化频数编码以及字符（默认为~）)
struct Weight {
	int count;
	string code;
	unsigned char ch;
	Weight(int cnt = 0, string cd = "", unsigned char cha = '~') {
		count = cnt;
		code = cd;
		ch = cha;
	}
};
Weight weights[256];  //结构体数组，里面储存结构体

//建立HUffman编码树(首先根据第一个非零频数的节点建堆（转化为节点类型入堆），使用tem2记录初始的i值（break），然后向堆中插入元素。将两个最小的节点合并为一个入堆，循环。)
template <typename E>
TreeNode<E>* generateHuffmanTree(E *arr, E invalid, int number) {//hp里面存地是节点类型
	const int cnt = 256;
	TreeNode<E>* tem1[256];
	//利用第一个非零元素建堆
	int tem2;
	for (int i = 0; i < number; i++)
	{
		if (arr[i].count != invalid.count) {
			tem1[0] = new TreeNode<E>(arr[i].count);
			tem1[0]->setIt(arr[i]);
			tem2 = i;
			break;
		}
	}
	minheap<TreeNode<E>*> hp(tem1, cnt, 1);
	//向堆中插入元素
	for (int i = 0; i < number; i++)
	{
		if ((arr[i].count != invalid.count) && (i != tem2)) {
			TreeNode<E>*temp = new TreeNode<E>(arr[i].count);
			temp->setIt(arr[i]);
			hp.insert(temp);
		}

	}
	//cout << "heap number is:" << hp.size() << endl;
	while (hp.size() > 1) {
		TreeNode<E> *temp1, *temp2, *temp3 = NULL;
		temp1 = hp.removefirst();  //pull first two node
		temp2 = hp.removefirst();  //off the list
		temp3 = new TreeNode<E>(temp1->weight() + temp2->weight());
		temp3->setLeft(temp1);
		temp3->setRight(temp2);
		hp.insert(temp3);
	}
	return hp.removefirst();
}

//打印哈夫曼编码树，验证节点正确(右、中、左的顺序递归打印)
template <typename E>
void printHMNode(TreeNode<E>*root, int n = 0) {
	if (root == NULL) return;
	printHMNode(root->right(), n + 1);
	for (int i = 0; i < n; i++)
	{
		cout << ' ' << ' ';
	}
	cout << root->value().ch << endl;
	printHMNode(root->left(), n + 1);
}
int numcount = 0;
int nummun = 0;
template <typename E>
//给字符节点编码(叶子节点时，将包含频数编码以及字符的Weight类型赋给当前节点；在weights中记录code值。根左右顺序递归)
void generateCode(TreeNode<E>* root, string code) {
	if (root) {
		numcount++;
		if (root->left() == NULL && root->right() == NULL) {
			nummun++;
			Weight temp; 
			temp.count = root->value().count;
			temp.code = code;
			temp.ch = root->value().ch;
			root->setIt(temp);
			//在weights里也记录code
			weights[temp.ch].code = code; 
		}
		generateCode(root->left(), code + "0");
		generateCode(root->right(), code + "1");
	}
}
//树结构的储存
void TransNode(TreeNode<Weight>* root,FILE* fConfig) {
	unsigned char a = '0';unsigned char b = '1';
	if (root) {
		if (root->left() == NULL && root -> right() == NULL) {
			fwrite(&a, sizeof(a), 1, fConfig);
		}
		else{ fwrite(&b, sizeof(b), 1, fConfig); }
		TransNode(root->left(), fConfig);
		TransNode(root->right(), fConfig);
	}
}
//叶子节点字符的储存
void TransChar(TreeNode<Weight>* root, FILE* fConfig) {
	if (root == NULL) return;
	if (root->left() == NULL && root->right() == NULL) {
		unsigned char cha = root->value().ch;
		cout << cha << endl;
		fwrite(&cha, sizeof(cha), 1, fConfig);
	}
	TransChar(root->left(), fConfig);
	TransChar(root->right(), fConfig);
}
//重建树（p是存的字符的数组）
TreeNode<Weight>* CreateTree(unsigned char** p, FILE* fConfig) {
	unsigned char ch = fgetc(fConfig);
	if ((**p == '\0') || ((ch != '0') && (ch != '1'))) 
		return NULL;
	else {
		TreeNode<Weight>* root;
		if (ch == '0')
		{
			Weight weit(0, "", (unsigned char)**p);
			(*p)++;
			root = new TreeNode<Weight>(0);
			root->setIt(weit);
			return root;
		}
		else if (ch == '1') root = new TreeNode<Weight>(0);
		root->setLeft(CreateTree(p, fConfig));
		root->setRight(CreateTree(p, fConfig));
		return root;
	}
}

//清空树(左右根顺序迭代删除节点)
template <typename E>
void ClearNode(TreeNode<E>* root) {
	if (root == NULL) return;
	ClearNode(root->left());
	ClearNode(root->right());
	delete root;
}

//压缩函数(定义三个文件指针以及总字符数，打开原文件，读入字符时（向weights中给相应字符赋频数以及字符，综字符数加一），建树，编码)
bool compress(const char* filename) {
	FILE* reader;
	FILE* writer;
	FILE* fConfig;
	int totalCount = 0;
	//打开原文件		
	fopen_s(&reader, filename, "rb");
	if (!reader) {
		cout << "源文件不存在,请检查源文件位置!" << endl;
		return false;
	}
	int ch;
	while ((ch = fgetc(reader)) != EOF) {
		weights[ch].count++;    //如何储存每个节点的频数？   使用结构体数组
		weights[ch].ch = ch;    //将weights中的各元素的ch值改为其位置/索引
		totalCount++;
	}
	Weight invalid;
	TreeNode<Weight>*root = generateHuffmanTree<Weight>(weights, invalid, 256);
	string code;
	generateCode(root, code);
	//打开压缩文件
	fopen_s(&writer, "E:/code//compress_file.txt", "wb");
	fseek(reader, 0, SEEK_SET);
	int pos = 0;
	unsigned char value = 0;
	//将Huffman编码写入压缩文件
	while ((ch = fgetc(reader)) != EOF) {
		string code = weights[ch].code;
		for (unsigned int i = 0; i <code.size(); i++) {
			value <<= 1;
			if (code[i] == '1') {
				value |= 1;
			}
			if (++pos == 8) {
				fputc(value, writer);
				value = 0;
				pos = 0;
			}
		}
	}
	//如果最后的编码没有8位,补齐8位编码
	if (pos) {
		value = value << (8 - pos);
		fputc(value, writer);
	}
	//将Human树内容相关内容写入配置文件中	 
	fopen_s(&fConfig, "E:/code//Config_file.txt", "wb");
	//储存totalcount
	fwrite(&totalCount, sizeof(totalCount), 1, fConfig);
	fwrite(&numcount, sizeof(numcount), 1, fConfig);
	fwrite(&nummun, sizeof(nummun), 1, fConfig);
	//储存树结构,储存叶子节点的字符
	TransNode(root, fConfig);
	TransChar(root, fConfig);
	//清空Huffman树
	ClearNode(root);
	//关闭文件
	fclose(reader);
	fclose(writer);
	fclose(fConfig);
	return true;
}
//解压缩函数(创建三个文件指针、一个Weight类型的数组，读文件，建树，解码)
bool uncompress(const char* filename) {
	//读取配置文件
	FILE* fCompress;
	FILE* fConfig;
	FILE* fUnCompress;
	int totalCount = 0;
	int numbercount = 0;
	unsigned char word[256]; 
	fopen_s(&fCompress, filename, "rb");  //打开压缩好的文件  
	if (!fCompress) {
		cout << "压缩文件不存在,请检查压缩文件位置!" << endl;
		return false;
	}
	fopen_s(&fConfig, "E:/code//config_file.txt", "rb");	//打开配置的文件 
	if (!fConfig) {
		cout << "配置文件不存在,请检查配置文件位置!" << endl;
		return false;
	}
	fopen_s(&fUnCompress, "E:/code//unCompress.txt", "wb"); //创建解压缩文件	 

	//将配置文件中的信息还原得到Huffman树
	fread(&totalCount, 4, 1, fConfig);
	fread(&numbercount, 4, 1, fConfig);
	fread(&nummun, 4, 1, fConfig);
	fseek(fConfig, numbercount+12 , SEEK_SET);  //跳过树结构的信息
	fread(&word, nummun, 1, fConfig);
	fseek(fConfig, 12, SEEK_SET);

	unsigned char*p1 = word;
	unsigned char** p = &p1;
	TreeNode<Weight>* root = CreateTree(p, fConfig);
	TreeNode<Weight>* cur = root;
	unsigned char ch = fgetc(fCompress);
	int pos = 8;    //解压缩的编码是根据每个字符是0还是1来遍历树，当遇到叶子节点就输出其值，再重新遍历树。
	while (1) {
		--pos;
		if ((ch >> pos) & 1) {
			cur = cur->right();
		}
		else {
			cur = cur->left();
		}
		if ((cur->left() == NULL) && (cur->right() == NULL)) {  
			fputc( cur->value().ch, fUnCompress);
			cout << cur->value().ch;
			cur = root;   //再次从根节点遍历  
			totalCount--;
		}
		if (pos == 0) {
			ch = fgetc(fCompress);
			pos = 8;
		}
		if (totalCount == 0) { //不读取压缩时为了凑够一个字节而加进去的比特位  
			break;
		}
	}
	//清空Huffman树
	ClearNode(root);
	//关闭文件
	fclose(fConfig);
	fclose(fCompress);
	fclose(fUnCompress);
	return true;
}
void main() {
  //if (compress("E:/code//experiment1.txt"))cout << "压缩成功" << endl;;   //压缩函数运行成功
	//if (uncompress("E:/code//compress_file.txt")) cout << "解压缩成功"<< endl;   //解压缩函数

	cout << "This is a self_designed compressor base on Huffman coding by group 6. " << endl;
	cout << "                                                                     李宬睿，侯牧村，吴盈" << endl;
	while (1)
	{
		cout << "We have thress operations:" << endl;
		cout << "1.压缩文件" << endl;
		cout << "2.解压文件" << endl;
		cout << "3.退出" << endl;
		cout << "请输入操作：" << endl;
		unsigned char ch;
		cin >> ch;
		if (ch == '1')
		{
			cout << "请输入目标文件路径：" << endl;
			//得到文件路径，读文件
			char path[100];
			cin >> path;
			if (compress((const char*)path)) {
				cout << "压缩完成" << endl;
			}
		}
		else if (ch == '2') {
			cout << "请输入目标文件路径：" << endl;
			//得到文件路径
			char path[100];
			cin >> path;
			if (uncompress((const char*)path)) {
				cout << "解压缩完成" << endl;
			}
		}
		else { break; }
	}
	cout << "已退出。\n感谢使用本压缩文件！" << endl;
	return;
}
