题目描述
======================
N  辆车沿着一条车道驶向位于 target 英里之外的共同目的地。
每辆车 i 以恒定的速度 speed[i] （英里/小时），从初始位置 position[i] （英里） 沿车道驶向目的地。
一辆车永远不会超过前面的另一辆车，但它可以追上去，并与前车以相同的速度紧接着行驶。
此时，我们会忽略这两辆车之间的距离，也就是说，它们被假定处于相同的位置。
车队 是一些由行驶在相同位置、具有相同速度的车组成的非空集合。注意，一辆车也可以是一个车队。
即便一辆车在目的地才赶上了一个车队，它们仍然会被视作是同一个车队。
会有多少车队到达目的地?

示例：

输入：target = 12, position = [10,8,0,5,3], speed = [2,4,1,1,3]
输出：3
解释：
从 10 和 8 开始的车会组成一个车队，它们在 12 处相遇。
从 0 处开始的车无法追上其它车，所以它自己就是一个车队。
从 5 和 3 开始的车会组成一个车队，它们在 6 处相遇。
请注意，在到达目的地之前没有其它车会遇到这些车队，所以答案是 3。

提示：

	0 <= N <= 10 ^ 4
	0 < target <= 10 ^ 6
	0 < speed[i] <= 10 ^ 6
	0 <= position[i] < target
	所有车的初始位置各不相同。

C语言解法
=====================
### ①
```c
typedef struct {
	int position;
	int speed;
	double time; // 时间用double好比较
} Car;

int cmp(const void *a, const void *b)
{
	Car *aa = (Car *)a;
	Car *bb = (Car *)b;
	return(((aa->position) > (bb->position)) ? 1 : -1);
}

int carFleet(int target, int* position, int positionSize, int* speed, int speedSize) {
	int i;
    if(speedSize == 0 || positionSize == 0) {
        return 0;
    }
	Car arr[50000];
	// 存储
	for (i = 0; i < positionSize; i++) {
		arr[i].position = position[i];
		arr[i].speed = speed[i];
		arr[i].time = (double)(target - position[i]) / speed[i];
	}
	// 按照位置排序
	qsort(arr, positionSize, sizeof(arr[0]), cmp);
	
	// 统计 
	int team = 0;
	for (i = positionSize - 1; i >= 0; i--) {
		while (i > 0 && arr[i - 1].time <= arr[i].time) { // 第i+1辆车能被第i辆车追上
            arr[i - 1].time = arr[i].time; // 更新成最慢速度
			i--;
		}
		team++;
	}
    if (team == 0) {
        return 1;
    }
	return team;
}
```
### ②
```c
#define ZERO 1e-6 //double型数值比较，通常设置一个比较精度
int comp(double *a, double *b){
    return *a > *b ? 1 : -1;
}
int carFleet(int target, int* position, int positionSize, int* speed, int speedSize){
    if(position == NULL || positionSize == 0) return 0;
    if(positionSize == 1) return 1; //0和1的情况
    int fleetNum = 1; //起始值设为1
    double car[positionSize][2];
    for(int i = 0; i < positionSize; i++){
        car[i][0] = position[i];
        car[i][1] = (double)(target - position[i]) / speed[i];  //计算时间
    }
    qsort(car, positionSize, sizeof(car[0]), comp);//排序

    for(int i = positionSize - 1; i > 0; i--){

        if(car[i][1] < car[i - 1][1]){
            fleetNum++;//i车时间小于于（i-1）车时，后面的车就追不上，车队加1
        }
        else if(car[i][1] > car[i - 1][1]){
            car[i - 1][1] = car[i][1];//i车时间大于（i-1）车时，会追上且被前面的车堵上，时间为i车时间，此时车队数不增加
        }
    }
    return fleetNum;
}
```
