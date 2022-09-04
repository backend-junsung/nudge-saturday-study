# [Codeforces Round #818 (Div. 2)](https://codeforces.com/contest/1717)

책이 회사에 있어 풀었던 코드포스로 대체한다. 2022-09-04

## 푼 문제

[A. Madoka and Strange Thoughts](https://codeforces.com/contest/1717/problem/A)

[B. Madoka and Underground Competitions](https://codeforces.com/contest/1717/problem/B)

[C. Madoka and Formal Statement](https://codeforces.com/contest/1717/problem/C)

## 풀지 못한 문제

[D. Madoka and The Corruption Scheme](https://codeforces.com/contest/1717/problem/D)

[E. Madoka and The Best University](https://codeforces.com/contest/1717/problem/E)

[F. Madoka and The First Session](https://codeforces.com/contest/1717/problem/F)

## A. Madoka and Strange Thoughts

### [문제](https://codeforces.com/contest/1717/problem/A)

최대공약수 함수 gcd, 최소공배수 함수 lcm

첫 번째 줄에 t가 주어지면, t개의 테스트 케이스에 대하여,

두 번째줄부터 각각 t번의 n이 주어지면, 1 <= a, b <= n에 대하여,
lcm(a, b) / gcd(a, b) <= 3을 만족하는 a, b의 개수를 구하라.

### 풀이

```
lcm(a, b) = a * b / gcd(a, b)
a = gcd(a, b) * i
b = gcd(a, b) * j
```

이다.
따라서,

```
lcm(a, b) / gcd(a, b) <= 3
```

을 변형하면,

```
i * j <= 3
```

이 된다.

따라서 만족하는 i, j는 (1, 1), (1, 2), (1, 3), (2,1)이 가능한다.
단, 이 때 gcd(i, j) \* MAX(i, j) <= 3을 만족하는 경우만 가능하다.

gcd(i, j) \* 3 <= n이라면 가능한 개수가 +5  
그렇지 않으며 gcd(i, j) \* 2 <= n이라면 가능한 개수가 +3  
그렇지 않으며 gcd(i, j) \* 1 <= n이라면 가능한 개수가 +1

따라서, gcd(i, j)가 1일 때부터 n일 때까지의 모든 경우의 수에 대하여, 위의 결과(카운트)를 적절히 늘려주면 된다.
gcd를 일일히 늘리며 해도 되며, 범위를 알고 있으므로 바로 계산식을 세워도 된다.

#### 오답

한 번 TLE가 나왔다.
A번이라고 딱히 수학적 생각 없이 반복문으로 풀릴 줄 알았으나, 아니었다.

### 제출 코드(cpp)

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <bits/stdc++.h>
using namespace std;

#define all(x) (x).begin(), (x).end()
#define endl "\n"
#define ends " "
#define pb(x) push_back(x)
#define fio()                     \
    ios_base::sync_with_stdio(0); \
    cin.tie(0)
#define fileio()                   \
    ifstream file_in("input.txt"); \
    ofstream file_out("output.txt")

#define double long double
typedef long long ll;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef tuple<int, int, int> tpi;
typedef tuple<ll, ll, ll> tpl;
typedef pair<double, ll> pdl;
typedef unsigned long long ull;

typedef priority_queue<int, vector<int>, greater<int>> miheap;
typedef priority_queue<int, vector<int>, less<int>> Miheap;
typedef priority_queue<pair<int, int>, vector<pair<int, int>>,
                       greater<pair<int, int>>>
    miiheap;
typedef priority_queue<pair<int, int>, vector<pair<int, int>>,
                       less<pair<int, int>>>
    Miiheap;

vector<string> split(const string& str, const string& delimeter) {
    vector<string> ret;
    int start = 0, end = 0;
    while ((end = str.find(delimeter, start)) != string::npos) {
        ret.push_back(str.substr(start, end - start));
        start = end + delimeter.length();
    }
    ret.push_back(str.substr(start));
    return ret;
}

int main() {
    fio();
    int t;
    cin >> t;
    while (t--) {
        int n;
        cin >> n;
        vector<int> a(n), b(n);

        bool res = true;

        for (int i = 0; i < n; i++) cin >> a[i];
        for (int i = 0; i < n; i++) {
            cin >> b[i];
            if (b[i] < a[i]) res = false;
        }

        if (a[n - 1] < b[n - 1] && b[n - 1] > b[0] + 1) res = false;

        for (int i = n - 1; i > 0; i--) {
            if (b[i - 1] > b[i] + 1 && b[i - 1] != a[i - 1]) res = false;
        }

        cout << (res ? "YES" : "NO") << endl;
    }
}
```

## B. Madoka and Underground Competitions

### [문제](https://codeforces.com/contest/1717/problem/B)

입력: t, n, k, r, c

첫 줄에 t가 들어온다. 이후 t개의 테스트 케이스가 들어온다.
2번째 줄부터, n k r c가 들어온다.

이 때, n \* n 크기의 지도가 모드 .으로 가득 차있다.
(r, c)번째는 반드시 X가 되어야한다. - 좌측 상단이 (1, 1)
또, 임의로 1 \* k 또는 k \* 1 크기의 범위를 잡으면, 항상 X가 적어도 하나 이상 포함되어야있어야 한다.
위 조건을 만족시키면서, 지도가 최소한의 개수의 X를 포함하도록 하는 지도를 각 TC별로 출력하라.

### 풀이

우선 r, c에 X를 채워야 한다.
거기에 X를 채워도 대각선 방향으로는 항상 X가 없으므로, 그 곳에도 채워준다.
그 다음, 채워준 곳으로부터 상하좌우로 k 칸 이동 시에, 거기에 X가 없다면, 그 곳을 기준으로도 대각선으로 X를 채워준다.

#### 오답

한 번 틀렸다.
지도를 벗어날 경우 채우지 않도록 예외처리하는 과정에서, >= 0이 아니라 > 0 으로 해버리며 범위가 변경되었다.

### 제출 코드(cpp)

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <bits/stdc++.h>
using namespace std;

#define all(x) (x).begin(), (x).end()
#define endl "\n"
#define ends " "
#define pb(x) push_back(x)
#define fio()                     \
    ios_base::sync_with_stdio(0); \
    cin.tie(0)
#define fileio()                   \
    ifstream file_in("input.txt"); \
    ofstream file_out("output.txt")

#define double long double
typedef long long ll;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef tuple<int, int, int> tpi;
typedef tuple<ll, ll, ll> tpl;
typedef pair<double, ll> pdl;
typedef unsigned long long ull;

typedef priority_queue<int, vector<int>, greater<int>> miheap;
typedef priority_queue<int, vector<int>, less<int>> Miheap;
typedef priority_queue<pair<int, int>, vector<pair<int, int>>,
                       greater<pair<int, int>>>
    miiheap;
typedef priority_queue<pair<int, int>, vector<pair<int, int>>,
                       less<pair<int, int>>>
    Miiheap;

vector<string> split(const string& str, const string& delimeter) {
    vector<string> ret;
    int start = 0, end = 0;
    while ((end = str.find(delimeter, start)) != string::npos) {
        ret.push_back(str.substr(start, end - start));
        start = end + delimeter.length();
    }
    ret.push_back(str.substr(start));
    return ret;
}

void mark(int n, int k, int r, int c, vector<vector<bool>>& map) {
    int offset = c - r;
    for (int i = 0; i < n; i++) {
        int tmp = (i + offset) % n;
        if (tmp < 0) tmp += n;
        map[i][tmp] = true;
    }

    if (r + k < n && !map[r + k][c]) mark(n, k, r + k, c, map);
    if (c + k < n && !map[r][c + k]) mark(n, k, r, c + k, map);
    if (r - k >= 0 && !map[r - k][c]) mark(n, k, r - k, c, map);
    if (c - k >= 0 && !map[r][c - k]) mark(n, k, r, c - k, map);
}

int main() {
    fio();

    int t;
    cin >> t;

    while (t--) {
        int n, k, r, c;
        cin >> n >> k >> r >> c;
        vector<vector<bool>> map(n, vector<bool>(n, false));

        mark(n, k, r - 1, c - 1, map);
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                cout << (map[i][j] ? 'X' : '.');
            }
            cout << endl;
        }
    }
}
```

## C. Madoka and Formal Statement

### [문제](https://codeforces.com/contest/1717/problem/C)

t개의 tc가 들어온다.
각 tc별로, 숫자 n이 주어진다.
그 다음, 줄마다 길이가 n인 배열 a와 b가 주어진다.

a를 다음과 같은 규칙을 통해 b로 만들 수 있는지 판별하라.

배열의 첫 번째 원소는 a<sub>1</sub>이다.
a<sub>i</sub> <= a<sub>i+1</sub>이라면, a<sub>i</sub>를 1 증가시킬 수 있다.(i < n)
a<sub>n</sub> <= a<sub>1</sub>이라면, a<sub>n</sub>을 1 증가시킬 수 있다.

만들 수 있다면, YES  
만들 수 없다면, NO  
를 출력하라.

### 풀이

1. a<sub>i</sub> > b<sub>i</sub>를 만족하는 경우가 있다면, 불가능하다.
2. b<sub>n</sub> > b<sub>1</sub> + 1이면서, a<sub>n</sub> < b<sub>n</sub>이 이라면, a<sub>n</sub>이 b<sub>n</sub>이 될 수 없으므로, 불가능하다.
3. b<sub>i + 1</sub> + 1 < b<sub>i</sub>이면서, b<sub>i</sub> < a<sub>i</sub>이라면, a<sub>i</sub>이 b<sub>i</sub>이 될 수 없으므로, 불가능하다.

위의 세 조건을 모두 통과했다면, 가능하다.

#### 오답

한 번 틀렸다.
이미 a[i] = b[i]인 경우를 고려하지 않아서(2, 3번 조건 중 두 번째로 서술한 조건)이다.

### 제출 코드(cpp)

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <bits/stdc++.h>
using namespace std;

#define all(x) (x).begin(), (x).end()
#define endl "\n"
#define ends " "
#define pb(x) push_back(x)
#define fio()                     \
    ios_base::sync_with_stdio(0); \
    cin.tie(0)
#define fileio()                   \
    ifstream file_in("input.txt"); \
    ofstream file_out("output.txt")

#define double long double
typedef long long ll;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef tuple<int, int, int> tpi;
typedef tuple<ll, ll, ll> tpl;
typedef pair<double, ll> pdl;
typedef unsigned long long ull;

typedef priority_queue<int, vector<int>, greater<int>> miheap;
typedef priority_queue<int, vector<int>, less<int>> Miheap;
typedef priority_queue<pair<int, int>, vector<pair<int, int>>,
                       greater<pair<int, int>>>
    miiheap;
typedef priority_queue<pair<int, int>, vector<pair<int, int>>,
                       less<pair<int, int>>>
    Miiheap;

vector<string> split(const string& str, const string& delimeter) {
    vector<string> ret;
    int start = 0, end = 0;
    while ((end = str.find(delimeter, start)) != string::npos) {
        ret.push_back(str.substr(start, end - start));
        start = end + delimeter.length();
    }
    ret.push_back(str.substr(start));
    return ret;
}

int main() {
    fio();
    int t;
    cin >> t;
    while (t--) {
        int n;
        cin >> n;
        vector<int> a(n), b(n);

        bool res = true;

        for (int i = 0; i < n; i++) cin >> a[i];
        for (int i = 0; i < n; i++) {
            cin >> b[i];
            if (b[i] < a[i]) res = false;
        }

        if (a[n - 1] < b[n - 1] && b[n - 1] > b[0] + 1) res = false;

        for (int i = n - 1; i > 0; i--) {
            if (b[i - 1] > b[i] + 1 && b[i - 1] != a[i - 1]) res = false;
        }

        cout << (res ? "YES" : "NO") << endl;
    }
}
```

## 공통 풀이

[에디토리얼](https://codeforces.com/blog/entry/105419)
