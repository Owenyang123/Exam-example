电话号码转换  题目描述

某语音翻译软件，需要实现如下中英文电话号码转换功能：

若输入的是英文数字单词或Double组成的电话号码，则输出对应的中文数字单词；
若输入为中文数字单词组成的电话号码，则输出对应的英文数字单词。
若输入不合法，则输出字符串ERROR。
中文数字、英文数字分别见下表：
中文数字单词：Yi Er San Si Wu Liu Qi Ba Jiu Ling
英文数字单词：One Two Three Four Five Six Seven Eight Nine Zero

说明：

输入保证每个单词都是合法的英文数字单词/中文数字单词/Double；
合法的电话号码要么全中文，不会含 Double ；要么全英文，可能含 Double；
若含 Double：
合法格式：其后必须跟随英文数字单词，代表两个该数字。如输入DoubleSix，代表 SixSix；
不合法的格式：其后跟随的不是英文数字单词，如 DoubleLiu 或 DoubleDouble 都是非法的。

输入
一行仅由大小写字母组成的字符串，非空且长度不大于500

输出
一个字符串，表示转换后的电话号码；若输入不合法，输出ERROR。

样例
  输入样例 1 

    SixOneThreeOneDoubleZero
    
  输出样例 1

    LiuYiSanYiLingLing

  输入样例 2 

    YiLingSanSanJiu
    
  输出样例 2

    OneZeroThreeThreeNine
    
  提示样例 2
  
  注意：SanSan不能转换为 DoubleThree

  输入样例 3 

    DoubleLiu
    
  输出样例 3

    ERROR

神TM的这道居然属于简单题...

python代码

    def translate(telephone_number: str) -> str:
        # 在此添加你的代码
        change = {
            "Yi": "One", "Er": "Two", "San": "Three", "Si": "Four", "Wu": "Five", "Liu":"Six", "Qi": "Seven",
            "Ba": "Eight",
            "Jiu": "Nine", "Ling": "Zero", "One": "Yi", "Two": "Er", "Three": "San", "Four": "Si", "Five": "Wu",
            "Six": "Liu",
            "Seven": "Qi", "Eight": "Ba", "Nine": "Jiu", "Zero": "Ling"
        }
        # 正则表达式来分离
        chinese_num_regex = re.compile(r'Yi|Er|San|Si|Wu|Liu|Qi|Ba|Jiu|Ling')
        eng_num_regex = re.compile(r'One|Two|Three|Four|Five|Six|Seven|Eight|Nine|Zero|Double')
        res_ch = chinese_num_regex.findall(telephone_number)
        res_eng = eng_num_regex.findall(telephone_number)
        # six会被si分解出来，然后我们通过比较两个列表的长度，如果是英语，那么英语列表一定比中文列表长，反之亦然
        if len(res_eng) >= len(res_ch):
            # 不要浪费之前开辟的空间
            res_ch.clear()
            # 其实这里使用range更好，但是由于公司代码规范，使用enumerate来执行
            for index, item in enumerate(res_eng):
                # 如果是double，那么就要注意了
                if item == "Double":
                    # double是最后一个——不合规，double后面不是数字——不合规
                    if index == len(res_eng)-1 or res_eng[index+1] == "Double":
                        return "ERROR"
                    res_ch.append(change[res_eng[index + 1]])
                else:
                    res_ch.append(change[item])
            return "".join(res_ch)
        else:
            res_eng.clear()
            # 中文里面不能有double
            if "Double" in telephone_number:
                return "ERROR"
            for item in res_ch:
                res_eng.append(change[item])
            return "".join(res_eng)

C++代码：

    #include <iostream>
    #include <string>
    #include <vector>
    #include <map>
    using namespace std;
    // 解题思路：主要考察字符串的处理及哈希表的灵活运用
    // @ 步骤一：将字符串分割操作后，放入nums容器中
    // @ 步骤二：判段是中文数字转换位英文数字，还是英文数字转换位中文数字；
    // @ 步骤三：分别完成中文数字转换为英文数字子函数，以及英文数字转换为中文数字子函数；
    class Solution {
    public:
        map<string, string> ETOC {{"One", "Yi"}, {"Two", "Er"}, {"Three", "San"},
                                 {"Four", "Si"}, {"Five", "Wu"}, {"Six", "Liu"},
                                 {"Seven","Qi"}, {"Eight","Ba"}, {"Nine", "Jiu"},
                                 {"Zero", "Ling"}};
        map<string, string> CTOE {{"Yi","One" }, {"Er","Two" }, {"San","Three"},
                                  {"Si","Four"}, {"Wu","Five"}, {"Liu","Six"},
                                  {"Qi","Seven"}, {"Ba","Eight"}, {"Jiu","Nine"},
                                  {"Ling","Zero"}};
        string HanZhuanYin(vector<string>& nums) {
            string ans;
            for (int i = 0; i < nums.size(); ++i) {
                if (nums[i] == "Double" || ETOC.count(nums[i]) == 1) {
                    return "ERROR";
                } else {
                    ans = ans + CTOE[nums[i]];
                }
            }
            return ans;
        }
        string YinZhuanHan(vector<string>& nums) {
            string ans;
            for (int i = 0; i< nums.size(); ++i) {
                if (CTOE.count(nums[i]) == 1) {
                    return  "ERROR";
                } else if (nums[i] == "Double") {
                    if (i == nums.size() - 1) {
                        return "ERROR";
                    } else if (nums[i + 1] == "Double") {
                        return "ERROR";
                    } else {
                        ans += ETOC[nums[i + 1]];
                    }
                } else {
                    ans += ETOC[nums[i]];
                }
            }
            return ans;
        }
        string Translate(const string &input)
        {
            vector<string> nums;
            string num;
            string result;
            bool C = false;
            num = input[0];
            for (int i = 1; i < input.size(); i++) {
                if (input[i] >= 'a' && input[i] <= 'z') {
                    num += input[i];
                } else {
                    nums.push_back(num);
                    num.clear();
                    num = input[i];
                }
            }
            nums.push_back(num);
            for (int i = 0; i < nums.size(); ++i) {
                if (CTOE.count(nums[i]) == 1) {
                    C = true;
                    break;
                }
            }
            if (C) {
                result = HanZhuanYin(nums);
            } else {
                result = YinZhuanHan(nums);
            }
            return result;
        }
    };
    int main()
    {
        string input;
        cin >> input;
        Solution solu;
        string result = solu.Translate(input);
        std::cout << result;
        return 0;
    }

