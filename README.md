# cx330
西邮数据课设
#include<iostream>
#include<string>
#include<vector>
#include<cstring>
using namespace std;
#define inf 0x3f3f3f3f                        /*无穷大*/
#define MAXSIZE 11                            /*最大景点数*/
int flag;                                     /*判断程序是否退出*/
//结构
typedef struct{
    int num;                                  /*景点编号*/
    string name;                              /*景点名称*/
    string information;                       /*景点简介*/
    string others;                            /*景点备注*/
}area;                                        /*结点*/
int dis[MAXSIZE],vis[MAXSIZE],pre[MAXSIZE];   /*dis:最短路径,vis:标记,pre:前驱*/
typedef struct{
    int to;                                   /*目的景点*/
    int len;                                  /*路线长度*/
}Edge;
vector <Edge> Graph[MAXSIZE];                 /*邻接表储存*/
//子函数
void Creat();                                 /*建立邻接表*/
void Show();                                  /*打印平面图*/
void QueryArea();                             /*查询景点信息*/
void Resive();                                /*修改*/
void QueryRoad();                             /*寻找最短路*/
void menu();                                  /*操作汇总*/
//具体
area Area[MAXSIZE]={
        {0,"---","----------"},
        {1,"基础教学楼","上课学习的必备场所"},
        {2,"图书馆","学习氛围良好"},
        {3,"篮球场","锻炼身体"},
        {4,"体育馆","运动器材丰富"},
        {5,"澡堂","沐浴清洁"},
        {6,"旭日苑","美食的汇聚地"},
        {7,"宿舍","休息的最好空间"},
};                     /*初始化各景点信息,初始没有备注*/
void Creat()
{ Graph[1].push_back(Edge{2,500});
    Graph[2].push_back(Edge{1,500});
 
    Graph[2].push_back(Edge{3,1000});
    Graph[3].push_back(Edge{2,1000});
 
    Graph[2].push_back(Edge{4,900});
    Graph[4].push_back(Edge{2,900});
 
    Graph[3].push_back(Edge{5,600});
    Graph[5].push_back(Edge{3,600});
 
    Graph[3].push_back(Edge{4,50});
    Graph[4].push_back(Edge{3,50});
 
    Graph[4].push_back(Edge{6,1200});
    Graph[6].push_back(Edge{4,1200});
 
    Graph[5].push_back(Edge{6,150});
    Graph[6].push_back(Edge{5,150});
 
    Graph[6].push_back(Edge{8,750});
    Graph[8].push_back(Edge{6,750});
 
    Graph[4].push_back(Edge{7,1500});
    Graph[7].push_back(Edge{4,1500});

};                       /*邻接表的初始化*/
void Show()
{
    cout<<"             ------------------------------------------------------"<<endl;
    cout<<"            |                    西邮平面图                        |"<<endl;
    cout<<"            |------------------------------------------------------|"<<endl;
    cout<<"            |                      北门                            |"<<endl;
    cout<<"            |                100m// ||                             |"<<endl;
    cout<<"            |        [1]基础教学楼  ||1000m                             |"<<endl;
    cout<<"            |                       ||                             |"<<endl;
    cout<<"            |                       ||                             |"<<endl;
    cout<<"            |                       ||                             |"<<endl;
    cout<<"            |                   [2]图书馆                          |"<<endl;
    cout<<"            |                       ||——————————[8]东区 |"<<endl;
    cout<<"            |                 200m//||                  ||         |"<<endl;
    cout<<"            |                    // ||1500m             ||         |"<<endl;
    cout<<"            |           [3]篮球场   ||   [4]体育场      ||         |"<<endl;
    cout<<"            |      100m//           ||                  ||         |"<<endl;
    cout<<"            |        [5]澡堂        ||                  ||         |"<<endl;
    cout<<"            |                       ||                  ||         |"<<endl;
    cout<<"            |                       ||—————————||         |"<<endl;
    cout<<"            |                     [6]旭日苑    [7]宿舍             |"<<endl;
}
void QueryArea()
{
    Show();
    cout<<"输入要查询的景点编号(1~10):";
    int n;
    cin>>n;
    if(n<1||n>10)
    {
        cout<<"输入的编号不合法!"<<endl;
        return;
    }
    cout<<"------------------------------------------------------"<<endl;
    cout<<"<<<<编号为"<<n<<"的景点"<<endl;
    cout<<"|名称: "<<Area[n].name<<endl;
    cout<<"|简介："<<Area[n].information<<endl;
    cout<<"|备注："<<Area[n].others<<endl;
    cout<<"------------------------------------------------------"<<endl;
}
void Resive()
{
    Show();
    cout<<"输入要修改的景点编号:";
    int n;
    cin>>n;
    if(n<1||n>10)
    {
        cout<<"输入的编号不合法!"<<endl;
        return;
    }
    cout<<"                           ------------------------"<<endl;
    cout<<"                          |      景点修改系统      |"<<endl;
    cout<<"                          |------------------------|"<<endl;
    cout<<"                          |      1--景点简介       |"<<endl;
    cout<<"                          |      2--修改备注       |"<<endl;
    cout<<"                          |      其他--无变化      |"<<endl;
    cout<<"                           ------------------------"<<endl;
    int op;
    cout<<"输入你的选择:";
    cin>>op;
    switch(op)
    {
        case 1:{
            string Information;
            cout<<"输入即将修改的内容:";
            cin>>Information;
            Area[n].information=Information;
            cout<<"<<<<修改成功"<<endl;
            }
            break;
        case 2:{
            string Others;
            cout<<"输入要修改的备注:";
            cin>>Others;
            Area[n].others=Others;
            cout<<"<<<<修改成功"<<endl;
            }
            break;
        default :cout<<"<<<<没有任何改动！"<<endl;
    }
    cout<<"------------------------------"<<endl;
}
void QueryRoad()
{
    Show();
    Creat();
    cout<<"输入起始点编号与目的地编号,空格隔开：";
    int s,e;
    cin>>s>>e;
    memset(dis,inf,sizeof(dis));
    memset(vis,0,sizeof(vis));
    memset(pre,0,sizeof(pre));
    dis[s]=0;                                 /*自身到自身距离置为0*/
    int k=MAXSIZE-1;                          /*MAXSIZE-1次查找*/
    while(k--)
    {
        int MAX=inf,from;
        for(int i=1;i<MAXSIZE;++i)
            if(!vis[i]&&dis[i]<MAX)
            {
                MAX=dis[i];
                from=i;
            }
        vis[from]=1;
        for(int i=0;i<Graph[from].size();++i)
        {
            int to=Graph[from][i].to;
            int len=Graph[from][i].len;
            if(!vis[to]&&dis[to]>dis[from]+len)
            {
                dis[to]=dis[from]+len;
                pre[to]=from;               /*记录前驱*/
            }
        }
    }
    cout<<Area[s].name<<"到"<<Area[e].name<<"的最短距离为"<<dis[e]<<"米"<<endl;
    cout<<"<<<<具体路径为："<<endl;
    int temp[MAXSIZE];
    k=1;
    temp[1]=e;
    while(pre[e])
    {
        temp[++k]=pre[e];
        e=pre[e];
    }
    cout<<Area[temp[k]].name;
    for(int i=k-1;i>=1;--i)
        cout<<"->"<<Area[temp[i]].name;
                                            /*打印具体路径*/
    cout<<endl;
    cout<<"-----------------------------------"<<endl;
}
void menu()
{
    cout<<"                           ------------------------"<<endl;
    cout<<"                          |  欢迎来到西邮导游系统  |"<<endl;
    cout<<"                          |------------------------|"<<endl;
    cout<<"                          |     1--查看平面图      |"<<endl;
    cout<<"                          |     2--修改景点信息    |"<<endl;
    cout<<"                          |     3--查看景点信息    |"<<endl;
    cout<<"                          |     4--查询最短路径    |"<<endl;
    cout<<"                          |     其他--退出         |"<<endl;
    cout<<"                           ------------------------"<<endl;
    int op;
    cout<<"输入你的选择：";
    cin>>op;
    switch(op)
    {
        case 1:Show();break;
        case 2:Resive();break;
        case 3:QueryArea();break;
        case 4:QueryRoad();break;
        default :flag=1;cout<<"<<<<欢迎下次再来!"<<endl;
    }
}
int main()
{
    while(1)
    {
        menu();
        if(flag)break;
        system("pause");
        system("cls");
    }
    return 0;
}
