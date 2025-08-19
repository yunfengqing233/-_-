# -_-
学习嵌入式的一些问答记录
现在我学会了变量和指针，一维数组和二维数组的指针，现在我能学函数指针吗？

可以的 👍
你现在已经学会了变量、指针和数组的指针，其实这些都是 理解函数指针的基础。

PS:对我而言，函数指针就是将原来需要一个个is-else来做判断做选择的事情，变成了像数组一样，通过地址（下标）直接调用，省去了做判断的功夫，修改起来代码量更少，理解之后也更直观，本质就是对if-else的简化

# 函数指针学习整理

## 1️⃣ 基础概念

- **函数指针**就是**存储函数地址的指针**。
- 写法示例：
```c
int (*fp)(int,int);   // 指向返回 int、参数为 (int,int) 的函数
fp = add;             // 指向函数 add
int result = fp(2,3); // 调用函数
```
- **函数名本身就是地址**：`add == &add`
- **数组名与函数名不同**：
  - `arr == &arr[0]` （数组名退化为首元素指针）
  - `&arr` 是整个数组地址，类型不同

---

## 2️⃣ 函数指针的核心价值

> 把函数当作数据传递，使程序更灵活、可扩展、可复用

- 主要用途：
  1. 回调函数（Callback）
  2. 函数数组 / 菜单调用
  3. 状态机（State Machine）
  4. 结构体 + 函数指针 → 模拟虚函数 / 多态
  5. 拓展场景：插件机制、事件驱动、算法可替换、任务调度

---

## 3️⃣ 应用示例与说明

### 3.1 回调函数（Callback）

- **概念**：把行为传给另一个函数，让它在内部调用
- **示例**：排序函数中传入不同比较规则
```c
int cmp_asc(int a,int b){ return a > b; }
int cmp_desc(int a,int b){ return a < b; }

void bubble_sort(int arr[], int n, int (*cmp)(int,int)){
    for(int i=0;i<n-1;i++){
        for(int j=0;j<n-1-i;j++){
            if(cmp(arr[j],arr[j+1])){
                int t=arr[j]; arr[j]=arr[j+1]; arr[j+1]=t;
            }
        }
    }
}
```
- **优点**：
  - 动态传递行为
  - 减少重复代码
  - 提高灵活性
- **不用函数指针的替代方法**：
```c
void bubble_sort(int arr[], int n, const char* order) {
    for(int i=0;i<n-1;i++){
        for(int j=0;j<n-1-i;j++){
            if((strcmp(order,"asc")==0 && arr[j]>arr[j+1]) ||
               (strcmp(order,"desc")==0 && arr[j]<arr[j+1])){
                int t=arr[j]; arr[j]=arr[j+1]; arr[j+1]=t;
            }
        }
    }
}
```
  - **优点**：简单，易理解  
  - **缺点**：判断开销、扩展性差，如果规则多就会变复杂  

---

### 3.2 函数数组 / 菜单调用

- **概念**：把多个函数地址存入数组，根据索引调用
- **示例**：
```c
void say_hello() { printf("Hello!\n"); }
void say_bye()   { printf("Bye!\n"); }

void (*actions[2])() = {say_hello, say_bye};

int choice;
scanf("%d",&choice);
if(choice>=0 && choice<2) actions[choice]();
```
- **优点**：
  - 减少 if/else 或 switch
  - 扩展功能简单
  - 行为可动态选择
- **不用函数指针的替代方法**：
```c
if(choice==0) say_hello();
else if(choice==1) say_bye();
```
  - **优点**：简单直接  
  - **缺点**：分支多时不易扩展，可读性差  

---

### 3.3 状态机（State Machine）

- **概念**：每个状态对应一个函数指针
- **示例**：
```c
void red()    { printf("红灯亮\n"); }
void green()  { printf("绿灯亮\n"); }
void yellow() { printf("黄灯亮\n"); }

void (*state)() = red;
state();       // 红灯
state = green;
state();       // 绿灯
```
- **优点**：
  - 状态切换直观
  - 减少条件判断
  - 易扩展
- **不用函数指针的替代方法**：
```c
if(state==RED) red();
else if(state==GREEN) green();
else if(state==YELLOW) yellow();
```
  - **优点**：易理解  
  - **缺点**：状态多时分支多，不够灵活  

---

### 3.4 结构体 + 函数指针（模拟虚函数）

- **概念**：结构体存数据 + 函数指针 → 模拟对象方法
- **示例**：
```c
typedef struct { void (*speak)(); } Animal;
void dog_speak() { printf("汪汪汪\n"); }
void cat_speak() { printf("喵喵喵\n"); }

Animal dog = {dog_speak};
Animal cat = {cat_speak};

dog.speak();
cat.speak();
```
- **优点**：
  - 实现多态
  - 统一接口，不同实现
  - 方便大型工程扩展
- **不用函数指针的替代方法**：
  - 每种动物写单独函数调用，没有统一接口  
  - **缺点**：增加对象类型时需要改调用代码，扩展性差  

---

## 4️⃣ 拓展场景

- 插件机制 / 动态加载
- 事件驱动系统（GUI、网络）
- 函数指针数组 + 循环表（任务调度、定时器）
- 算法可替换（排序、搜索、压缩、加密等）

---

## 5️⃣ 总结

- **核心作用**：把函数当作数据传递，让程序更灵活、可扩展、可复用
- **什么时候用**：
  - 行为需要动态选择
  - 统一接口，不同实现
  - 提高扩展性和可复用性
- **思路**：把函数名当作“指针”，可以存储、传递、动态调用
- **不用函数指针时**：
  - 条件判断简单直接，但扩展性差、代码重复多、效率稍低

