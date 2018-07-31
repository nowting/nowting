# nowting
ac自动机
#include<iostream>
#include<cmath>
#include <cstring>
#include<cstdio>
#include <cstring>
#include<algorithm>
#include <vector>
#include <map>
using namespace std;
const int maxn= 1e7+5;
const int MAX=10000000;
int cnt;
/////////----
//在字典树建立之前，先定义字典树上节点的结构体变量
struct node
{
    node *next[26];
    node *fail;
    int sum;//这个节点是不是一个单词的结尾，以及相应的个数

};
node *root;
char key[70];
node *q[MAX];
int head,tail;
node *newnode;
char text[maxn];
int N;
//字典树的建立
void Insert(char *s)
{
    node *p=root;
    for(int i=0;s[i];i++)
    {
        int x=s[i]-'a';
        if(p->next[x]==NULL)//如果为空就插入
        {
            newnode=(struct node *)malloc(sizeof(struct node));
            for(int j=0;j<26;j++) newnode->next[j]=0;
            newnode->sum=0;
            newnode->fail=0;
            p->next[x]=newnode;//将next构建出来
        }
        p=p->next[x];
    }
    p->sum++;
}
//基于队列（bfs）实现的,构造fail指针
void build_fail_pointer()
{
    head=0;
    tail=1;
    q[head]=root;
    node *p;
    node *temp;
    while(head<tail)
    {
        temp=q[head++];
        for(int i=0;i<=25;i++)
        {
            if(temp->next[i])
            {
                if(temp==root)
                {
                    temp->next[i]->fail=root;
                }
                else
                {
                    p=temp->fail;
                    while(p!=NULL)
                    {
                        if(p->next[i])
                        {
                            temp->next[i]->fail=p->next[i];
                            break;
                        }
                        p=p->fail;
                    }
                    if(p==NULL) temp->next[i]->fail=root;
                }
                q[tail++]=temp->next[i];

            }

        }
    }
}
void ac_automation(char *ch)
{
    node *p=root;
    int len=strlen(ch);
    for(int i=0;i<len;i++)
    {
        int x=ch[i]-'a';
        while(!p->next[x]&&p!=root) p=p->fail;//匹配失败，通过fail指针
        p=p->next[x];
        if(!p) p=root;
        node *temp=p;
        while(temp!=root)
        {
            if(temp->sum >=0)
            {
                cnt+=temp->sum;
                temp->sum=-1;
            }
            else break;
            temp=temp->fail;

        }
    }
}

int main()
{
    int t;
    scanf("%d",&t);
    while(t--)
    {
        root=(struct node *)malloc(sizeof(struct node));
        for(int j=0;j<26;j++) root->next[j]=0;
        root->fail=0;
        root->sum=0;
        scanf("%d",&N);
        getchar();
        for(int i=1;i<=N;i++)
        {
            scanf("%s",key);
            //gets(key);
            Insert(key);
        }
       scanf("%s",text);
      // gets(text);
        cnt=0;
        build_fail_pointer();
        ac_automation(text);

        printf("%d\n",cnt);
    }
    return 0;
}
