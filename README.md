# 9.14-
#include<stdlib.h>
#include<string>
#include<iostream>
#include<ctime>
#include<windows.h>
#include<conio.h>
#include<fstream>
using namespace std;
static int I = 0;//猪圈序号
static int i = 0;//猪的序号
static string a = "黑猪";
static string b = "小花猪";
static string c = "大花白猪";
static string d = "空";
static double price = 0;
static int soldpignum=0;

ofstream OutFile1("SoldPig.txt");//卖掉的猪文本
ofstream OutFile2("CurrentPig.txt");//退出时猪圈的所有猪
ofstream OutFile3("Input-Littlepig.txt");//引入的所有猪仔

typedef struct Pig{//猪
    int pigstynum;
    int num;
    char *type;
    double weight;
    double time;
    double pri;
    bool ill;
}pig;
static Pig soldpig[1000];

typedef struct Pigsty{//猪圈
    int no;
    int pignum;
    int black;
    struct node *a;
    int illpignum;
}pigsty;

typedef struct Node//猪圈指针
{
    pigsty Data;
    struct Node *next;
}Lnode;

typedef struct node//猪指针
{
    pig data;
    struct node *next;
}lnode;

void Clean(Lnode *p){//将猪圈清空
    Lnode *r = p;
    r->Data.black = 0;
    r->Data.no = 0;
    r->Data.pignum = 0;
    r->Data.illpignum = 0;
}

void clean(lnode *p){//将猪槽清空
    lnode *r = p;
    r->data.num = 0;
    r->data.pigstynum = 0;
    r->data.time = 0;
    r->data.type = &d[0];
    r->data.weight = 0;
    r->data.ill = false;
}

void creat(lnode *hh,int n)//生成猪槽
{
    lnode *p,*r;
    int j;
    r=hh;
    for(j=0;j<n;j++)
    {
        p=(lnode *)malloc(sizeof(lnode));
        r->next=p;
        clean(r);
        r->data.pigstynum = I;
        r->data.num = i;
        i++;
        if(i==10) i = i - 10;
        r=p;
    }
    I++;
    r->next=NULL;
}

void Creat(Lnode *b,int n)//生成猪圈
{
    Lnode *p,*r;
    int j;
    r=b;
    for(j=0;j<n;j++)
    {
        p=(Lnode *)malloc(sizeof(Lnode));
        r->next=p;
        Clean(r);
        r->Data.no = I;
        lnode *hh;
        hh=(lnode *)malloc(sizeof(lnode));
        hh->next=NULL;
        r->Data.a = hh;
        creat(hh,10);
        r=p;
    }
    r->next=NULL;
}

void Input(Lnode *p,pig u)//放1猪进猪圈
{
    for (int j = 0; j < 10;j++){
        Lnode *r = p;
        for (; r->next != NULL; r = r->next)
        {
            if (r->Data.pignum <= j){
                if (r->Data.pignum==0){
                    if (u.type==&a[0]) r->Data.black = 1;
                    lnode *h = r->Data.a;
                    for (; h->next != NULL; h = h->next)
                    {
                        if(h->data.weight == 0&&r->Data.illpignum==0)
                        {
                        h->data.weight = u.weight;
                        h->data.type = u.type;
                        h->data.pigstynum = r->Data.no;
                        h->data.time = u.time;
                        h->data.ill = u.ill;
                        r->Data.pignum++;
                        return;
                        }
                    }
                }
                if(((r->Data.pignum!=0&&r->Data.black==0)&&(u.type==&b[0]||u.type==&c[0]))||(r->Data.black==1&&u.type==&a[0])){
                    lnode *h = r->Data.a;
                    for (; h->next != NULL; h = h->next)
                    {
                        if(h->data.weight == 0&&r->Data.illpignum==0)
                        {
                        h->data.weight = u.weight;
                        h->data.type = u.type;
                        h->data.pigstynum = r->Data.no;
                        h->data.time = u.time;
                        h->data.ill = u.ill;
                        r->Data.pignum++;
                        return;
                        }
                    }
                }      
            }
        }
    }
}

void output(Lnode *h)//输出所有猪圈
{
    Lnode *p;
    p = h;
    while(p->next!=NULL)
    {
        if(p->Data.pignum!=0){
            cout<<p->Data.no<<endl;
            lnode *r = p->Data.a;
            while(r->next!=NULL){
                if(r->data.weight!=0){
                cout << r->data.num << r->data.type << r->data.weight <<"kg"<<r->data.time<< "天";
                if(r->data.ill==false) cout << "正常 ";
                else if(r->data.ill==true) cout << "患病 ";}
                r=r->next;
            }
            cout << endl;
        }
        p=p->next;
    }
}

void OutPut(Lnode *h)//输出所有猪圈
{
    Lnode *p;
    p = h;
    while(p->next!=NULL)
    {
        if(p->Data.pignum!=0){
			OutFile2<<p->Data.no<<endl;
            lnode *r = p->Data.a;
            while(r->next!=NULL){
                if(r->data.weight!=0){
                OutFile2 << r->data.num << r->data.type << r->data.weight <<"kg"<<r->data.time<< "天";
                if(r->data.ill==false) OutFile2 << "正常 ";
                else if(r->data.ill==true) OutFile2 << "患病 ";}
                r=r->next;
            }
            OutFile2 << endl;
        }
        p=p->next;
    }
}

void ergoPW(Lnode *h){//遍历并增加每头猪1天的体重
    Lnode *p;
    p = h;
    while (p->next != NULL)
    {
        lnode *r = p->Data.a;
        for (;r->next != NULL ; r = r->next)
        {
            if(r->data.weight!=0){
                double x;
                x = double((rand() % 12) / 10.00);
                r->data.weight = r->data.weight + x;
                r->data.time++;
            }  
        }
        p = p->next;
    }
    Sleep(100);     
}

void onlyergo(Lnode *h,int n){//找到对应猪圈并输出这个猪圈的猪情况
    Lnode *p = h;
    while (p->Data.no!=n){
        p = p->next;
    }
    lnode *r = p->Data.a;
    if(p->Data.pignum==0)
        cout << "这个猪圈暂时没有养猪哦" << endl;
    else{
        cout << "===============================" << endl;
        while(r->next!=NULL){
            if(r->data.weight!=0){
                cout << r->data.num << r->data.type << " ";  
            }
            r = r->next;
        }
        cout << endl;
        cout << "         查看第几头猪" << endl;
        cout << "===============================" << endl;
        int y;
        cin >> y;
        r = p->Data.a;
        while(r->next!=NULL){
            if(r->data.num==y) {
                if(r->data.weight!=0)
                    cout << r->data.num << r->data.type << r->data.weight << "kg" << r->data.time << "天 ";
                else
                    cout << "您搜索的猪不存在哦" << endl;
                break;
            }
            r = r->next;
        }
        cout << endl;
    }
}

void raceergo(Lnode *h,char *g){//种族分布
    Lnode *p;
    p = h;
    int racenum=0;
    int rr;
    while(p->next!=NULL)
    {   
        lnode *r = p->Data.a;
        rr = 0;
        if(p->Data.pignum!=0){
            while(r->next!=NULL){
                if(r->data.weight!=0&&r->data.type==g){
                    racenum++;
                    rr++;
                }
                r=r->next;
            }
        }
        if(rr!=0){
            cout<<p->Data.no<<endl;
            r = p->Data.a;
            while(r->next!=NULL){
                if(r->data.weight!=0&&r->data.type==g){
                    cout << r->data.num << r->data.type << r->data.weight <<"kg"<<r->data.time<< "天 ";
                }
                r=r->next;
            }
            cout << endl;
        }
        p=p->next;
    }
    cout << "一共有" << racenum << "头" << *g << endl;
}

void pricesum(Lnode *h){//计算所有出圈猪的价格并清空出圈猪槽
    Lnode *p;
    p = h;
    while (p->next != NULL)
    {
        lnode *r = p->Data.a;
        int j = 0;
        for (;r->next != NULL; r = r->next)
        {
            if((r->data.time>=365||r->data.weight>75.00)&&r->data.ill==false){
                soldpig[soldpignum].time = r->data.time;
                soldpig[soldpignum].type = r->data.type;
                soldpig[soldpignum].weight = r->data.weight;
                soldpig[soldpignum].pigstynum = r->data.pigstynum;
                soldpig[soldpignum].num = r->data.num;
                if (r->data.type == &a[0]){
                    price = price + r->data.weight * 30;
                    soldpig[soldpignum].pri = r->data.weight * 30;
                }
                if (r->data.type == &b[0]){
                    price = price + r->data.weight * 14;
                    soldpig[soldpignum].pri = r->data.weight * 14;
                }   
                if (r->data.type == &c[0]){
                    price = price + r->data.weight * 12;
                    soldpig[soldpignum].pri = r->data.weight * 12;
                }
				OutFile1 << soldpig[soldpignum].pigstynum << "号猪圈" << soldpig[soldpignum].num << "号" << soldpig[soldpignum].type << soldpig[soldpignum].weight << "kg" << soldpig[soldpignum].time << "天 卖了" << soldpig[soldpignum].pri << "元" << endl;
				soldpignum++;    
                r->data.time = 0;
                r->data.type = &d[0];
                r->data.weight = 0;
                j++;
            }
        }
        p->Data.pignum = p->Data.pignum - j;
        if(p->Data.pignum==0) p->Data.black = 0;
        p = p->next;
    }
}

bool Full(Lnode *h){//看看猪圈满了没有
    Lnode *p;
    p = h;
    while(p->next!=NULL)
    {
        lnode *r = p->Data.a;
        while(r->next!=NULL){
            if(r->data.weight==0) return false;
            r=r->next;
        }
        p=p->next;
    }
    return true;
}

void random(pig *u){//随机一个猪
    double wei;
    wei=rand() % 30+20.00;
    u->weight = wei;
    int tpnum;
    tpnum = rand() % 3;
    switch(tpnum){
        case 0:
            u->type = &a[0];
            break;
        case 1:
            u->type = &b[0];
            break;
        case 2:
            u->type = &c[0];
            break;
    };
    u->time = 0;
    u->ill = false;
}

void sourcedisease(Lnode *h,int n,int m){//设置一个病源猪
    Lnode *p = h;
    for (int j = 0; j < n;j++){
        p = p->next;
    }
    p->Data.illpignum++;
    lnode *r = p->Data.a;
    for (int d = 0; d < m;d++){
        r = r->next;
    }
    r->data.ill = true;
}

void outputill(Lnode *h){//显示猪圈猪的位置供用户选择
    Lnode *p;
    p = h;
    while(p->next!=NULL)
    {
        if(p->Data.pignum!=0){
            cout<<p->Data.no<<"号猪圈"<<" ";
            lnode *r = p->Data.a;
            while(r->next!=NULL){
                if(r->data.weight!=0)
                cout << r->data.num << " ";
                r=r->next;
            }
            cout << endl;
        }
        p=p->next;
    }
}

static int contagionda;
void infectedpig(Lnode *h,int n){//感染本猪圈和相邻猪圈
    int contagion=2;
    double loss;
    Lnode *p;
    p = h;
    for (int j = 0; j < n; j++)
    {
        p = p->next;
    }
    lnode *r = p->Data.a;
    for (; r->next != NULL; r = r->next)
        {
            if (r->data.weight != 0 && r->data.ill == false)
            {
                int t = rand() % 100;
                if (t < 50)
                {
                    p->Data.illpignum++;
                    r->data.ill = true;
                    contagion = 1;
                    if (r->data.type == &a[0])
                    {
                        loss = r->data.weight * 30;
                    }
                    if (r->data.type == &b[0])
                    {
                        loss = r->data.weight * 14;
                    }
                    if (r->data.type == &c[0])
                    {
                        loss = r->data.weight * 12;
                    }
                    cout << r->data.pigstynum << "号猪圈" << r->data.num << r->data.type << "被感染 "
                         << "损失" << loss << "元" << endl;
                }
            }
        }
    if(n!=99){
        r = p->next->Data.a;
        for (; r->next != NULL; r = r->next)
        {
            if(r->data.weight!=0&&r->data.ill==false){
                int t = rand() % 100;
                if (t < 15)
                {
                    p->Data.illpignum++;
                    r->data.ill = true;
                    contagion = 1;
                    if (r->data.type == &a[0])
                    {
                        loss = r->data.weight * 30;
                    }
                    if (r->data.type == &b[0])
                    {
                        loss = r->data.weight * 14;
                    }
                    if (r->data.type == &c[0])
                    {
                        loss = r->data.weight * 12;
                    }
                    cout << r->data.pigstynum <<"号猪圈"<< r->data.num << r->data.type << "被感染 "<< "损失" << loss << "元" << endl;
                }
            }
        }
    }
    p = h;
    if(n!=0){
        for (int j = 0; j < n - 1; j++)
        {
            p = p->next;
        }
        r = p->Data.a;
        for (; r->next != NULL; r = r->next)
        {
            if(r->data.weight!=0&&r->data.ill==false){
                int t = rand() % 100;
                if (t < 15)
                {
                    p->Data.illpignum++;
                    r->data.ill = true;
                    contagion = 1;
                    if (r->data.type == &a[0])
                    {
                        loss = r->data.weight * 30;
                    }
                    if (r->data.type == &b[0])
                    {
                        loss = r->data.weight * 14;
                    }
                    if (r->data.type == &c[0])
                    {
                        loss = r->data.weight * 12;
                    }
                    cout << r->data.pigstynum <<"号猪圈"<< r->data.num << r->data.type << "被感染 "<< "损失" << loss << "元" << endl;
                }
            }
        }
    }
    if(contagion==1) contagionda = 1;
    else contagionda = 0;
}

static int head=0;
static int back=99;
void illpigsty(Lnode *h){//计算感染的猪圈数
    Lnode *p;
    p = h;
    while(p->next!=NULL){
        int k = 0;
        lnode *r;
        r = p->Data.a;
        while(r->next!=NULL){
            if(r->data.ill==true&&r->data.weight!=0){
                head = p->Data.no;
                k = 1;
                break;
            }
            r = r->next;
        }
        if(k==1) break;
        p = p->next;
    }
    while(p->next!=NULL){
        lnode *r;
        r = p->Data.a;
        int j = 0;
        while(r->next!=NULL){
            if(r->data.ill==false&&r->data.weight!=0){
                j++;
            }
            r = r->next;
        }
        if(j==p->Data.pignum) {
            back = p->Data.no - 1;
            break;
        }
        p = p->next;
    }
}

bool allill(Lnode *h){//判断是否全部感染
    Lnode *p;
    p = h;
    while(p->next!=NULL){
        lnode *r;
        r = p->Data.a;
        while(r->next!=NULL){
            if(r->data.weight!=0&&r->data.ill==false){
                return false;
            }
            r = r->next;
        }
        p = p->next;
    }
    return true;
}

void allhealthy(Lnode *h){//把所有猪都设为没病
    Lnode *p;
    p = h;
    while(p->next!=NULL){
        lnode *r;
        r = p->Data.a;
        p->Data.illpignum = 0;
        while(r->next!=NULL){
            if(r->data.weight!=0){
                r->data.ill = false;
            }
            r = r->next;
        }
        p = p->next;
    }
}

void dangerControl(Lnode *h){//风险控制
    Lnode *p;
    p = h;
    int J = 0,f=0;
    lnode *r;
    while(p->next!=NULL){
        r = p->Data.a;
        while(r->next!=NULL){
            if(r->data.ill==true){
                f = 1;
                break;
            }
            r = r->next;
        }
        if(f==1){
            Lnode *pp;
            pp = h;
            for (int jj = 0; jj < J-1;jj++){
                pp = pp->next;
            }
            int x = 0;
			double Pric = 0;
            while(pp->next!=NULL&&x<3){
                pp->Data.illpignum = 1;
                r = pp->Data.a;
                while(r->next!=NULL){
                    if(r->data.weight!=0&&r->data.ill==false){
                        if (r->data.type == &a[0])
                        {
                            Pric = Pric + r->data.weight * 30;
                        }
                        else if (r->data.type == &b[0])
                        {
                            Pric = Pric + r->data.weight * 14;
                        }
                        else if (r->data.type == &c[0])
                        {
                            Pric = Pric + r->data.weight * 12;
                        }
                        r->data.time = 0;
                        r->data.type = &d[0];
                        r->data.weight = 0;
                        r->data.ill = false;
                        pp->Data.pignum--;
                    }
                    r = r->next;
                }
                x++;
                pp = pp->next;  
            }
            cout << "=====================" << endl;
            if(J==0) cout << "隔离了"<< J <<" "<< J + 1 << "号猪圈" << endl;
            else if(J==99) cout << "隔离了" << J - 1 <<" "<< J << "号猪圈" << endl;
            else cout << "隔离了" << J - 1 <<" "<< J <<" "<< J + 1 << "号猪圈" << endl;
            cout << "卖出" << Pric << "元" << endl;
            cout << "=====================" << endl;
            f = 0;
        }
        J++;
        p = p->next;
    }
}

int main()
{
	int xx = 1;
    int button;
    while(xx){  
        cout << "               养猪模拟" << endl;
        cout << "======================================" << endl;
        cout << "             1.开始养猪" << endl;
        cout << "             2.养猪规则" << endl;
        cout << "             3.退出游戏" << endl;
        cout << "======================================" << endl;
        cin >> button;
        while(button!=1&&button!=2&&button!=3){
            cout << "您输入的数字不正确哦，请重新输入" << endl;
            cin >> button;
        }
        if(button==1) xx = 0;
        if(button==2){
            cout << "================================================================" << endl;
            cout << "您是一个农场主，您一共有100个猪圈（按照0-99编号），每个猪圈最多" << endl;
            cout << "养10头猪（按照0到9编号），猪一共有三个品种：黑猪、小花猪和大花白" << endl;
            cout << "猪。由于黑猪好斗，将为之单独开辟猪圈。每3个月，养猪场会出圈一批猪" << endl;
            cout << "，包括：体重超过150斤的和饲养超过1年的；同时补充一批猪崽儿（猪的" << endl;
            cout << "品种、数量录入或者选择随机生成）。猪的初始体重在20-50kg,而每一头" << endl;
            cout << "猪的每日体重增长0.0-1.2kg之间（游戏中1s等于一天）。猪将会均匀分，" << endl;
            cout << "布在100个猪圈里，我们会为您统计您的猪总体售价。（黑猪15元一斤，小" << endl;
            cout << "花猪7元一斤，大花白猪6元一斤）。" << endl;
            cout << "                     按任意键返回游戏" << endl;
            cout << "=================================================================" << endl;
            getch();
        }
        if(button==3) return 0;
    }
    Lnode *p;
    p=(Lnode *)malloc(sizeof(Lnode));
    p->next = NULL;
    Creat(p,100);
    srand((int)time(0));
    int n = rand() % 500;
    while(n==0){
        n = rand() % 500;
    }
    pig u;
    for (int j = 0; j < n;j++){
        random(&u);
        Input(p, u);
    }
    cout << "==============================" << endl;
    cout << "          开始养猪" << endl;
    cout << "           养猪中。。。"<<endl;
    cout << "  按任意键可暂停查询猪圈情况" << endl;
    cout << "==============================" << endl;
    int days = 1;
    while (1)
    {   ergoPW(p);
        if (days % 90 == 0)
        {
            pricesum(p);
            int littlepig = rand() % 100;
            for (int jj = 0; jj < littlepig; jj++)
            {
                if(Full(p)) break;
                random(&u);
				OutFile3 << u.type << u.weight <<"kg ";
				if(u.ill==false) OutFile3 << "正常"<<endl;
                else if(u.ill==true) OutFile3 << "患病"<<endl;
				Input(p, u);
            }
        }
        cout <<"第"<< days <<"天 ";
        if(days%5==0) cout<<endl;
        if (_kbhit()){//如果有按键按下，则_kbhit()函数返回真，头文件conio.h
            getch();//键盘输入，不会显示
            int ff;
            while(ff){
                cout << endl;
                cout << "====================================" << endl;
                cout << "               选项：" << endl;
                cout << "           1.猪圈总览" << endl;
                cout << "           2.查看猪圈" << endl;
                cout << "           3.种族分布" << endl;
                cout << "           4.已售的猪" << endl;
                cout << "           5.继续养猪" << endl;
                cout << "           6.猪瘟模拟" << endl;
                cout << "           7.退出游戏" << endl;
                cout << "====================================" << endl;
                cin >> button;
                if (button == 1){
                    cout << "这是第"<<days<<"天的所有猪圈情况：" << endl;
                    output(p);
                }
                else if (button == 2){
                    cout << "选择猪圈编号：0-99" << endl;
                    cin >> button;
                    while(button>99||button<0){
                        cout << "您输入的选项不正确，请重新输入" << endl;
                        cin >> button;
                    }
                    cout << "这是第" << days << "天的第" << button << "个猪圈的情况：" << endl;
                    onlyergo(p, button);
                }
                else if(button==3){
                    while(1){
                        cout << "==================================" << endl;
                        cout << "      你想查看哪一种族分布情况" << endl;
                        cout << "             1.黑猪" << endl;
                        cout << "             2.小花猪" << endl;
                        cout << "             3.大花白猪" << endl;
                        cout << "             4.返回" << endl;
                        cout << "==================================" << endl;
                        int sr;
                        cin >> sr;
                        if(sr==4) break;
                        switch(sr){
                            case 1:raceergo(p, &a[0]);
                                break;
                            case 2:raceergo(p, &b[0]);
                                break;
                            case 3:raceergo(p, &c[0]);
                                break;
                        }
                        if(sr!=1&&sr!=2&&sr!=3&&sr!=4) cout << "您输入的选项不正确哦" << endl;
                        cout << "按任意键返回上一界面" << endl;
                        getch();
                    }
                }
                else if (button == 4){
                    cout << "卖猪情况：" << endl;
                    int kk;
                    for (int jj = 0; jj < 100;jj++){
                        kk = 0;
                        for (int rr = 0; soldpig[rr].weight != 0;rr++){
                            if (soldpig[rr].pigstynum == jj){
                                if(kk==0){
                                    cout << jj << endl;
                                    kk = 1;
                                }
                                cout<< soldpig[rr].num <<"号"<< soldpig[rr].type << soldpig[rr].weight <<"kg"<< soldpig[rr].time << "天 卖了"<<soldpig[rr].pri<<"元"<<endl;
                            }
                        }  
                    }
                    if(price==0)
                        cout << "您还未有合格的猪卖出哦" << endl;
                    else{
                        cout << endl;
                        cout << "一共卖出：" << price << "元" << endl;
                    }
                }
                else if(button==5) break;
                else if(button==6){
                    cout << "这是目前所有的猪" << endl;
                    outputill(p);
                    allhealthy(p);
                    cout << "请选择病源猪" << endl;
                    cout << "输入格式：猪圈序号(0-99)  " << endl;
                    cout << "          猪的序号(0-9)" << endl;
                    int n, m;
                    cin >> n >> m;
                    sourcedisease(p, n, m);
                    cout << "下面为您实时播报感染情况" << endl;
                    int jj = 1;
                    for (;allill(p)!=true;jj++){
                        days++;
                        ergoPW(p);
                        int contagionDa = 0;
                        illpigsty(p);
                        for (int rr = head; rr <= back;rr++){
                            infectedpig(p, rr);
                            if(contagionda==1) contagionDa = 1;
                        }
                        if(contagionDa==1) {
                            cout << "第" << days << "天" << endl;
                            cout << "=====================================" << endl;
                        }
                        Sleep(100);
                        if(days%90==0){
                            pricesum(p);
                        }
                        double uu=0;
                        if (_kbhit()){//如果有按键按下，则_kbhit()函数返回真，头文件conio.h
                            getch();//键盘输入，不会显示
                            int yy = 1;
                            while(yy){
                                cout << "========================="<<endl;
                                cout <<  "    是否进行风险控制" << endl;
                                cout << "           1.是" << endl;
                                cout << "           2.否" << endl;
                                cout << "           3.帮助" << endl;
                                cout << "========================="<<endl;
                                cin >> button;
                                if(button==1){
                                    dangerControl(p);
                                    yy = 0;
                                    cout << "========================================" << endl;
                                    cout << "      已成功进行风险控制，目前猪圈正常" << endl;
                                    cout << "                。。。。。" << endl;
                                    cout << "========================================" << endl;
                                    cout << "                按任意键继续" << endl;
                                    uu = 1.876;
                                }
                                else if(button==2){
                                    cout << "按任意键可继续进行" << endl;
                                    yy = 0;
                                }
                                else if(button==3){
                                    cout << "===================================" << endl;
                                    cout << "我们将会以卖掉病猪圈周围猪圈的健康猪" << endl;
                                    cout << "并不再将新猪放进这些猪圈来进行防控。" << endl;
                                    cout << "              1.返回" << endl;
                                    cout << "===================================" << endl;
                                    cin >> button;
                                    if(button==1) yy=1;
                                }
                            }
                            getch();
                        }
                        if(uu==1.876) cout << "..........耐心等待，因为无感染所以不显示，别乱点.........." << endl;
                    }
                    cout << "卖出健康猪" << price << "元" << endl;
                    cout << "至此所有猪都感染猪瘟,历时"<<jj<<"天" << endl;
                    return 0;
                }
                else if(button==7) {OutPut(p);return 0;}
                else{
                    cout << "您输入的选项不正确哦" << endl;
                }
                if(button!=3){
                    cout << "按任意键返回上一界面" << endl;
                    getch();
                }
                ff = 1;
            }
            cout << "按任意键可继续养猪" << endl;
            getch();
            cout << "==============================" << endl;
            cout << "            养猪中。。。" << endl;
            cout << "   按任意键可暂停查询猪圈情况" << endl;
            cout << "==============================" << endl;
        }
        days++; 
    }
	return 0;
}
