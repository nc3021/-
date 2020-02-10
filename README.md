# -
by C-Free
#include <stdio.h>
#include <stdlib.h>
#include <conio.h>
#include <windows.h>
#include <time.h>	

#define high 20  // 游戏画面尺寸
#define width 30
#define enemyNum 5 // 敌机个数
#define NAME_LEN 50
#define MAX_PARTS 100
#define TIME_LENGTH 10
#define IDENTIFY_LENGTH 1000
#define FILE_NAME  "壮志凌云.txt"

// 全局变量
struct part{
 
long int name;
double overtime[TIME_LENGTH];
int finalscore;
}date;
//存入一个date结构（包含玩家用户名 两个时间 得分）
int plane_blood=5;
FILE *fp;//定义一个file类型指针
int blood_position_x,blood_position_y;
int position_x,position_y; // 飞机位置
int enemy_x[enemyNum],enemy_y[enemyNum];  // 敌机位置
int canvas[high][width] = {0}; // 二维数组存储游戏画布中对应的元素
                        // 0为空格，1为飞机*，2为子弹|，3为敌机@
int score; // 得分
int EnemyMoveSpeed; // 敌机移动速度
clock_t start_time, finish_time;
int cord_time;


//排序 
int yonghuxinxi[2000][3]={0};
int huanchongyonghuxinxishuzu[2000][3]={0};
int dingweishu=0;
int yonghuminglinshicunchu;
int  huanchongyonghuxinxi; 
int huanchongscore;
int huanchongshijian;



void gotoxy(int x,int y)  //光标移动到(x,y)位置
{
    HANDLE handle = GetStdHandle(STD_OUTPUT_HANDLE);
    COORD pos;
    pos.X = x;
    pos.Y = y;
    SetConsoleCursorPosition(handle,pos);
}

void HideCursor() // 用于隐藏光标 
{
	CONSOLE_CURSOR_INFO cursor_info = {1, 0};  // 第二个值为0表示隐藏光标
	SetConsoleCursorInfo(GetStdHandle(STD_OUTPUT_HANDLE), &cursor_info);
}
void startup() // 数据初始化
{
	position_x = high-1;
	position_y = width/2;
	canvas[position_x][position_y] = 1;
	int k;
	for (k=0;k<enemyNum;k++)
	{
		enemy_x[k]= rand()%2;
		enemy_y[k]= rand()%(width-2);
		canvas[enemy_x[k]][enemy_y[k]+1] = 3;
	}
	score = 0;
	HideCursor();
}

void show()  // 显示画面
{
	gotoxy(0,0);// 光标移动到原点位置，以下重画清屏
	int i,j;
	for (i=0;i<high+2;i++)
	{
		for (j=0;j<width;j++)
		{
		  if (canvas[i][j]==1)
				printf("*");   //   输出飞机*
			else if (canvas[i][j]==2)
				printf("+");   //   输出子弹+
			else if (canvas[i][j]==3)
				printf("#");   //  输出敌方炮弹# 
			else if ((j==width-1)||(j==0))		//打印边界 
                printf("|");
            else if ((i==high)&&((j!=0)||(j!=width-1)))
                printf("*");
            else if ((i==high+1)&&((j!=0)||(j!=width-1)))
                printf("=");
            else printf(" ");
		}
		printf("\n");
	}
	printf("得分：%d\n血量:",score);
	switch (plane_blood)
	  {case 5: printf("_____\n    |*****|");
	           break;
       case 4: printf("_____\n    |**** |");
               break;
       case 3: printf("_____\n    |***  |");
               break;
       case 2: printf("_____\n    |**   |");
               break;
       case 1: printf("_____\n    |*    |");
               break;
	   case 0: printf("_____\n    |     |");
	           break;
  	}
}	

int updateWithoutInput()  // 与用户输入无关的更新
{
	int i,j,k,m,bool_k;
	for (i=0;i<high;i++) 
	{
		for (j=0;j<width;j++)
		{
			if (canvas[i][j]==2) 
			{
				for (k=0;k<enemyNum;k++)
				{
					if ((i==enemy_x[k]) && (j==enemy_y[k]))  // 子弹击中敌机
					{
						score++;                // 分数加1
						canvas[enemy_x[k]][enemy_y[k]] = 0;
						enemy_x[k] = rand()%2; // 产生新的飞机
						enemy_y[k] = rand()%width;
						canvas[enemy_x[k]][enemy_y[k]] = 3;
						canvas[i][j] = 0;     // 子弹消失
					}
					
	}//if
	
				// 子弹向上移动NMB
				canvas[i][j]=0;
				if (i>0)
				canvas[i-1][j]=2;
			}
		}

	}
	
	
	
	static int speed = 0;
	if (speed<EnemyMoveSpeed)
		speed++;

	for (k=0;k<enemyNum;k++)
	{
		if (plane_blood==0)  // 基地血量等于0 
		{
			finish_time = clock();
			cord_time = (double)(finish_time - start_time) ;
			printf("基地被毁！\n");
			printf("时间：%d s\n", (cord_time/1000));	
			Sleep(300);
			char BJtime1;//第一个是变量 第二个是结构中的数组

date.overtime[20]=cord_time;//第一个是游戏时间 第二个是结构
date.finalscore=score;//第一个是游戏得分 第二个是结构


printf("用户名\t游戏进行时间s\t玩家得分\n");
//printf("% s    ",date.name );
//printf("%s  ",BJtime1);
//printf("%d   ",cord_time) ;
//printf("%d   ",score) ;
printf("%d\n%d\n%d\n", date.name,cord_time/1000,score);
/*游戏结束之后，将玩家的游戏数据打印出来*/ 
fprintf(fp,"%d\n%d\n%d\n",date.name,cord_time/1000,date.finalscore);
/*将数据存入txt文本框里边*/ 

//fclose(fp);

			system("pause");//是暂停的意思，等待用户信号；不然控制台程序会一闪即过，你来不及看到执行结果。
			return 1;		
	}
		
	if ((position_x==enemy_x[k]) && (position_y==enemy_y[k]))  // 敌机撞到我机
		{
			finish_time = clock();
			cord_time = (double)(finish_time-start_time);
			printf("你被击毁！\n");
			printf("时间：%d s\n", cord_time/1000);
			printf("用户名\t游戏进行时间s\t玩家得分\n");
			printf("%d\n%d\n%d\n", date.name,cord_time/1000,score);	
			Sleep(300);
			system("pause"); 
			return 1;	
		}
		if (enemy_x[k]>high)   // 敌机跑出显示屏幕
		{
			canvas[enemy_x[k]][enemy_y[k]] = 0;
			enemy_x[k] = rand()%2;           // 产生新的飞机
			enemy_y[k] = rand()%width;
			canvas[enemy_x[k]][enemy_y[k]] = 3;
			plane_blood--;  // 减血 
		}

		if (speed == EnemyMoveSpeed)
		{
			// 敌机下落 
			for (k=0;k<enemyNum;k++)
			{
				canvas[enemy_x[k]][enemy_y[k]] = 0;
				enemy_x[k]++;			
				speed = 0;
				canvas[enemy_x[k]][enemy_y[k]] = 3;
			}
		}	
	}//for
/*函数结尾*/}

void updateWithInput()  // 与用户输入有关的更新
{
	char input;
	if(kbhit()) // 功能及返回值：检查当前是否有键盘输入，若有则返回一个非0值，否则返回0,所在头文件:conio.h
	{
		input = getch();//getch(): 所在头文件:conio.h 函数用途:从控制台读取一个字符,但不显示在屏幕上 
		if (input == 'a' && position_y>1) 
		{
			canvas[position_x][position_y] = 0;
			position_y--;  // 位置左移
			canvas[position_x][position_y] = 1;
		}
		else if (input == 'd' && position_y<(width-2))
		{
			canvas[position_x][position_y] = 0;
			position_y++;  // 位置右移
			canvas[position_x][position_y] = 1;
		}
		
		else if (input == ' ')  // 发射子弹
		{
			int Q = position_y;
			if (Q<0)
				Q = 0;
			int k;
			for (k=Q;k<=position_y;k++) // 发射闪弹
				canvas[position_x-1][k] = 2; // 发射子弹的初始位置在飞机的正上方
		}		
	}
}

void  spacekey()
{
	int a;
    int b=0;
	for (a=0;a<=50;a++)
   {
	if(a<50)
	  printf(" ");
    else
	if(a=50&&b<=10)
	  {printf(" \n");
      a=0;
	  b++;}
     else{
	 printf("\n"); 
	 break;}	 
    } 
}//输屏幕上方的一大片空白 
void space_continue() 
{
	char c;
    for (;;)
	{c=getch();
     if (c==' ')
	{system("cls");//清屏 
     break;}
	 else {
	 spacekey();
	 spacekey();
	 printf("             请按空格继续游戏"); }
     
     }
}

void clear_one(){//数据清除 
	int i=0,j=0;
	plane_blood=5;
	score=0;
	canvas[position_x][position_y]=4;
	position_x=high-1;
	position_y=width/2;
	canvas[high-1][width/2]=1;
}

int main()
{
	int k=0,v=0;
	int m,mmod;
	char ch1,ch2,ch3;
	int command_date;//录入用户输入的数据 
	int identify[IDENTIFY_LENGTH+1];//这里存的是用户的所有数据（Aname,Atime,Ascore,Bname,Btime,Bscore....)

    int p;
//	FILE *fp;//定义一个file类型指针
	if(!(fp=fopen(FILE_NAME,"a+"))){
	printf("Can't open C:\\Users\\12577\\Documents\\Tencent Files\\1257730824\\FileRecv\n"); //如果打不开的话，就返回失败 
	return 0;
}
while(1){
system("color 30");
 system("mode con cols=60 lines=35");
spacekey();

printf("                       壮志凌云\n");//第一界面 
spacekey();
printf("    a：向左移动 d：向右移动  空格：发射子弹");//还需游戏说明并需加足够的空格
printf("\n         请将输入法切换至英文模式");
printf("\n         请将输入法切换至英文模式\n(重要的事情说两遍)");
printf("\n             按空格键继续...") ;
HideCursor(); 
space_continue();
AGAIN:
spacekey();
printf("          输入你喜欢的一个名字(五位数字):");
scanf(" %d",&date.name);
for(;date.name>99999||date.name<10000;) {
  printf("          输入你喜欢的一个名字(五位数字):");
  scanf(" %d",&date.name);
}
space_continue();
//通过循环开始// 
//{//
void show();
//判断游戏结束后//
spacekey();

	printf("          输入e-简单模式\n          输入m-普通模式\n          输入h-困难模式\n");
	while(1)
	{
	if((ch1=getch())=='e'){
	EnemyMoveSpeed=20;
	break;
	}
	else if(ch1=='m'){
	EnemyMoveSpeed=15;
	break;
	}
	else if(ch1=='h'){
	EnemyMoveSpeed=10;
	break;
	}
	else 
	printf("          请输入正确指令!\n");
	}
	
	printf("       ——输入s开始游戏 ——\n");	
	while(1)
	{
	if((ch2=getch())=='s'){
    system("cls");
	break;}
	else
	printf("          请输入正确指令!\n");
	}
     start_time = clock();
	 system("color 8B"); 
	 if(k==0)                                       
	 startup();					
 	 while(1)			
	 {
	 	int o=0;
		gotoxy(0,0);
		show();				
        o=updateWithoutInput();// 与用户输入无关的更新
	    updateWithInput();  // 与用户输入有关的更新
	    if(o==1)
	    break;
	 }

   
    system("cls");
    spacekey();
    
    printf("              输入c再来一局\n              输入o结束游戏\n ");
    while(1){
     if((ch3=getch())=='c'){
   	system("cls");//清屏 
   	k=1;
   	clear_one();
    goto AGAIN;
	}
    if((ch3=getch())=='o'){
   	printf("                          游戏结束\n\n                 犹豫就会败北，果断就会白给\n\n");
   	 FILE *file = fopen(FILE_NAME, "r"); 
	
	if(file==NULL){
	printf("Can't open %s\n",FILE_NAME); //如果打不开的话，就返回失败 
	return 0;
}



for(p=0;p<IDENTIFY_LENGTH+1;p++){
	fscanf(file,"%d",&identify[p]);
	if(identify[p]>99999){
		p--;
		break;
	}
    }
fclose(file);

for(int i=0;i<p;i=i+3){
	huanchongyonghuxinxishuzu[i/3][0]=identify[i];
		huanchongyonghuxinxishuzu[i/3][1]=identify[i+1];
	huanchongyonghuxinxishuzu[i/3][2]=identify[i+2];
}//数据复制 
yonghuxinxi[0][0]=huanchongyonghuxinxishuzu[0][0];
yonghuxinxi[0][1]=huanchongyonghuxinxishuzu[0][1];
yonghuxinxi[0][2]=huanchongyonghuxinxishuzu[0][2];
/*printf("%d",yonghuxinxi[0][0]);*/

for(int i=1;i<=p/*p*/;i++){//这里我实在没办法，那个p不是已存数据个数，它恒定等于1001 
for(;dingweishu<i;dingweishu++)
{
if(huanchongyonghuxinxishuzu[i][2]>yonghuxinxi[dingweishu][2])
break;
	}
	
    for(int j=i;j>dingweishu;j--){ 
    yonghuxinxi[j-1][0]= yonghuxinxi[j-2][0];
    yonghuxinxi[j-1][1]= yonghuxinxi[j-2][1];
    yonghuxinxi[j-1][1]= yonghuxinxi[j-2][2];
    } 
    yonghuxinxi[dingweishu][0]= huanchongyonghuxinxishuzu[i][0] ;
    yonghuxinxi[dingweishu][1]= huanchongyonghuxinxishuzu[i][1];
    yonghuxinxi[dingweishu][2]= huanchongyonghuxinxishuzu[i][2];
  dingweishu=0;   
}




//输出排序末尾 
printf("       前三排行榜     \n");
for(int i=0;i<3;i++)
{
	printf("第%d名:%d\n",i+1,yonghuxinxi[i][0]);
	printf(" 时间:%d\n",yonghuxinxi[i][1]);
	printf(" 得分:%d\n",yonghuxinxi[i][2]);
}


Sleep(3000);//显示足够时间 
system("cls");
spacekey();

int command_date;//录入用户输入的数据 
printf("请输入你想要查询的数据：\n（仅支持玩家的名字、时间、分数）"); 
scanf("%d",&command_date);

    for(;;){ 
	  for(m=0;m<1000;m++){
	   if(command_date==identify[m]){
	   mmod=m%3;
	   switch(mmod){
   		case 0:{//说明余数是0，则第一个是名字 
		   	printf("用户名：%d\n",identify[m]);//打印名字 
		   	printf("时间：%d s\n",identify[m+1]);//打印时间 
		   	printf("分数： %d 分\n",identify[m+2]);//打印分数 
		   	break; 
   		}
   		case 1:{//说明余数是1，则第一个是时间 
		   	printf("用户名：%d\n",identify[m-1]);//打印名字 
		   	printf("时间：%d s\n",identify[m]);//打印时间 
		   	printf("分数: %d 分\n",identify[m+1]);//打印分数 
		   	break; 
		   }
   		case 2:{//说明余数是2，则第一个是分数 
		   	printf("用户名：%d\n",identify[m-2]);//打印名字 
		   	printf("时间：%d s\n",identify[m-1]);//打印时间 
		   	printf("分数: %d 分\n",identify[m]);//打印分数 
		   	break;
		   }
	    }
	   }
	  }
	    
		break;
	}
 
    return 0;
    }
   else
   	printf("请输入正确指令!\n");
    }
}
}
