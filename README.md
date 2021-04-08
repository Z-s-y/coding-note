# coding-experience-for-leetcode

python input EOFError:python3 stipulate the input function just can be called in father process

Bitmap:！！

<--------2021-04-06-------->

洗牌算法：

有一个大小为100的数组，里面的元素是1到100，怎样随机从里面选择50个数。

如果采用暴力算法，那么到后面选出来的随机数可能会在前面就已经出现过，使用set去重也会造成很大的时间复杂度开销。

洗牌算法的核心思想就是，对下标进行随机选择，按照顺序交换当前下标和随机下标里面的数字。这样即使随机到曾经出现的数也不会造成什么本次操作失效。

上述方法每一位只能出现99个数，虽然概率相等但是不够随机。可以将对下标的随机范围包括自己，这样每个数出现在某一位上的概率都是1/100。

在常数时间内完成向数组插入、删除、随机选取一个数的操作。

核心办法：通过hash函数完成元素值到下标的映射，这样在检索就可以通过hash映射在常数时间内找到对应值的索引，完成查找这一动作。

随机选取一个数可以直接对数组操作，通过随机下标的方式选出随机数。

在C++中，unordered_map采用STL标准库提供的hash函数进行map映射，该hash函数只适用于基本数据类型（包括string类型），并不能使用在自定义结构体或者类上面。容器模板中还表明了相等比较规则，改规则仅支持可以使用==符号进行比较的数据类型。

unordered_map类型的初始化为：

std::unordered_map<std::int,std::int> M_map;
成员函数：

begin()和end()都是返回迭代器；

empty()，若容器为空，返回true；否则false；

size()，返回容器存有的键值对的个数；

可以通过value=map[key]直接通过key值访问value值；

find()，查找以key为键的键值对，如果找到返回迭代器；否则==end()；

insert()，插入新的键值对；emplace()，向容器添加新的键值对，效率比前者高;

erase()，删除指定键值对；

clear()，清空容器；

swap()，交换两个容器的键值对，保证容器的类型完全相同。

题目如下：

设计一个支持在平均 时间复杂度 O(1) 下，执行以下操作的数据结构。

insert(val)：当元素 val 不存在时，向集合中插入该项。
remove(val)：元素 val 存在时，从集合中移除该项。
getRandom：随机返回现有集合中的一项。每个元素应该有相同的概率被返回。

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/insert-delete-getrandom-o1

代码如下：

/** 执行用时：44 ms, 在所有 C++ 提交中击败了81.33%的用户
    内存消耗：22.1 MB, 在所有 C++ 提交中击败了52.10%的用户 */


    /** Initialize your data structure here. */
    RandomizedSet() {}
    
    /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
    bool insert(int val) {
        if(mMap.find(val) == mMap.end()){
            mMap.emplace(val,mVec.size());
            mVec.push_back(val);
            return true;
        }
        return false;
    }
    
    /** Removes a value from the set. Returns true if the set contained the specified element. */
    bool remove(int val) {
        if(mMap.find(val) == mMap.end()){
            return false;
        }
        //可以直接删除映射值，将最后一个值赋给当前pos值，并且更新最后一个值的映射索引
        //原值可以不处理，因为需要删去，内容并不重要！
        int pos = mMap[val];
        //需要考虑本身就是最后一个值的情况
        mVec[pos] = mVec[mVec.size()-1];
        mMap[mVec[pos]] = pos;
        mVec.pop_back();
        mMap.erase(val);
        return true;
    }
    
    /** Get a random element from the set. */
    int getRandom() {
        return mVec[rand()%mVec.size()];
    }

    private:
      unordered_map<int,int> mMap;
      vector<int> mVec;
      
      
      
      
<--------2021-04-07-------->

非经典二分题：

二分题需要注意的细节是：必须能够跳出循环，一般判断在left<=right里面执行程序即可。这道非经典二分与经典二分的差别是在判断有序数组部分需要注意边界条件的情况，或者说左右边界在转移的时候需要考虑是取mid，还是mid左右端点。在这道题目里面如果将中点作为分割点，判断左边不包含mid元素的子数组是否有序，需要判断mid-1>=left是否成立，这里注意取等号，因为很显然会分割出一个元素的情况。如果左边数组是有序数组，判断两端点是否能够包含target值，这时候nums[left]<=target<=nums[right]也需要取等号，因为此前只判断了nums[mid]与target的大小情况，极端的看，也是出现一个元素时需要进行有效判断。如果左边子数组不是有序数组，那么右边数组肯定是有序数组（有可能两边都是有序数组，这是mid正好是旋转点或者原数组尾的情况；但是我们可以保证肯定有一边是有序数组），对右边数组进行上面相同操作，判断左右边界的转移情况。这种写法比包含mid的方法快，可能是转移过程剔除mid元素得到加速效果。

题目如下：

整数数组 nums 按升序排列，数组中的值互不相同 。

在传递给函数之前，nums 在预先未知的某个下标 k（0 <= k < nums.length）上进行了 旋转，使数组变为 [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]（下标从 0 开始计数）。例如，[0,1,2,4,5,6,7] 在下标 3 处经旋转后可能变为 [4,5,6,7,0,1,2] 。

给你旋转后的数组 nums 和一个整数 target ，如果 nums 中存在这个目标值 target ，则返回它的下标，否则返回 -1 。

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array

代码如下：

/** 执行用时：4 ms, 在所有 C++ 提交中击败了79.03%的用户
    内存消耗：10.8 MB, 在所有 C++ 提交中击败了59.32%的用户*/

    int left = 0;
    int right = nums.size()-1;

    while(left <= right){
        int mid = (left + right) / 2;
        //printf("%d,%d,%d,%d\n",left,mid,right,nums[mid]==target);
        //printf("%d,%d\n",target,nums[mid]);
        if(nums[mid] == target){
            return mid;
        } 
        if(nums[left] <= nums[mid]){
            if(nums[mid] >= target && nums[left] <= target){
                right = mid;
                //printf("1\n");
            }else{
                left = mid + 1;
                //printf("2\n");
            }
        }else{
            if(nums[mid] <= target && nums[right] >= target){
                left = mid;
                //printf("3\n");
            }else{
                right = mid - 1;
                //printf("4\n");
            }
        }
        //printf("%d,%d,%d\n",left,mid,right);
    }
    return -1;





