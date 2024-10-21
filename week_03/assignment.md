### References

- https://cs50.harvard.edu/x/2024/psets/3/
- https://manual.cs50.io/

### Note

어려운 문제가 있으면 푸는걸 위주로 하려 했는데, [Tideman](https://cs50.harvard.edu/x/2024/psets/3/tideman/#problem-to-solve)은 난이도가 꽤 높은 편이라, 쉬운버전인 Runoff로 풀었음.

Walkthrough를 몇 번 더 보면 이해히고 풀 수 있을 것 같은데, 강의에서도 도전적인 옵션이라는거 보면 난이도가 꽤 있어보여서 스킵함.

어느정도 구현을 해 두고 잘 나뉘어진 함수만 요구사항에 맞게 풀면 되서 편했다.

다만 실습에 정렬 구현이 없고 이진 탐색도 없는데, 이건 좀 아쉽다. (나중에 따로 공부할꺼니까 상관없겠지만)


```markdown
sort1,2,3 실행파일(컴파일됨)은 다음 알고리즘으로 구현되어 있음.

- Selection Sort
- Bubble Sort
- Merge Sort

`.txt`의 경우, 여러 형식으로 정렬되어 있고, 실행 파일의 입력으로 사용할 수 있다.

`time [실행파일] [input txt]` 명령어를 호출하여 실행 계획을 분석하고 말하면 됨.

일단 Merge Sort는 실행 시간이 nlogn으로 제일 빠르므로 random 으로 파악 가능.
Selection Sort은 이미 정렬된 상태에서도, 항상 모든 요소를 반복한다는 걸 생각하면, sorted 를 해도 속도가 느린 하나가 Selection Sort, 나머지가 Bubble Sort임.

```
time ./sort1 random50000.txt

real    0m5.717s
user    0m4.963s
sys     0m0.316s

---
time ./sort2 random50000.txt

real    0m0.712s
user    0m0.041s
sys     0m0.229s

---
time ./sort3 random50000.txt
real    0m2.494s
user    0m1.877s
sys     0m0.234s
```

위 결과로 sort2가 Merge Sort임을 확인

```
time ./sort1 sorted50000.txt
real    0m0.595s
user    0m0.022s
sys     0m0.240s

---
time ./sort3 sorted50000.txt

real    0m2.962s
user    0m1.776s
sys     0m0.222s
```

위 결과로 sort1이 정렬된 상태에서 빠르므로 Bubble Sort, 나머지 sort3가 Selection Sort임을 확인
```

```c 
plurality.c

#include <cs50.h>
#include <stdio.h>
#include <string.h>

// Max number of candidates
#define MAX 9

// Candidates have name and vote count
typedef struct
{
    string name;
    int votes;
} candidate;

// Array of candidates
candidate candidates[MAX];

// Number of candidates
int candidate_count;

// Function prototypes
bool vote(string name);
void print_winner(void);

int main(int argc, string argv[])
{
    // Check for invalid usage
    if (argc < 2)
    {
        printf("Usage: plurality [candidate ...]\n");
        return 1;
    }

    // Populate array of candidates
    candidate_count = argc - 1;
    if (candidate_count > MAX)
    {
        printf("Maximum number of candidates is %i\n", MAX);
        return 2;
    }
    for (int i = 0; i < candidate_count; i++)
    {
        candidates[i].name = argv[i + 1];
        candidates[i].votes = 0;
    }

    int voter_count = get_int("Number of voters: ");

    // Loop over all voters
    for (int i = 0; i < voter_count; i++)
    {
        string name = get_string("Vote: ");

        // Check for invalid vote
        if (!vote(name))
        {
            printf("Invalid vote.\n");
        }
    }

    // Display winner of election
    print_winner();
}

// Update vote totals given a new vote
bool vote(string name)
{
    // 이거 반복 말고 다른 좋은 방법이 있을거 같은데,
    // 진짜 MAP 쓰고싶네
    bool voted = false;
    for (int i = 0, len = candidate_count; i < len; i++)
    {
        if (strcmp(candidates[i].name, name) == 0)
        {
            voted = true;
            candidates[i].votes += 1;
        }
    }
    return voted;
}

// Print the winner (or winners) of the election
void print_winner(void)
{
    // 근데 이거 정렬 안해도 되는거 아닌가.
    // 걍 제일 높은 수 N 반복해서 구하고,
    // 제일 높은 수인 애들 출력하면 되는거 아님? 순서는 상관없으니까.
    // 그게 시간복잡도 제일 낮은데, 2N으로
    int max_votes_count = 0;
    for (int i = 0, len = candidate_count; i < len; i++)
    {
        if (candidates[i].votes > max_votes_count)
        {
            max_votes_count = candidates[i].votes;
        }
    }

    for (int i = 0, len = candidate_count; i < len; i++)
    {
        if (candidates[i].votes == max_votes_count)
        {
            printf("%s\n", candidates[i].name);
        }
    }

    return;
}

```

```c
runoff.c

#include <cs50.h>
#include <stdio.h>
#include <string.h>

// Max voters and candidates
#define MAX_VOTERS 100
#define MAX_CANDIDATES 9

// preferences[i][j] is jth preference for voter i
int preferences[MAX_VOTERS][MAX_CANDIDATES];

// Candidates have name, vote count, eliminated status
typedef struct
{
    string name;
    int votes;
    bool eliminated;
} candidate;

// Array of candidates
candidate candidates[MAX_CANDIDATES];

// Numbers of voters and candidates
int voter_count;
int candidate_count;

// Function prototypes
bool vote(int voter, int rank, string name);
void tabulate(void);
bool print_winner(void);
int find_min(void);
bool is_tie(int min);
void eliminate(int min);

int main(int argc, string argv[])
{
    // Check for invalid usage
    if (argc < 2)
    {
        printf("Usage: runoff [candidate ...]\n");
        return 1;
    }

    // Populate array of candidates
    candidate_count = argc - 1;
    if (candidate_count > MAX_CANDIDATES)
    {
        printf("Maximum number of candidates is %i\n", MAX_CANDIDATES);
        return 2;
    }
    for (int i = 0; i < candidate_count; i++)
    {
        candidates[i].name = argv[i + 1];
        candidates[i].votes = 0;
        candidates[i].eliminated = false;
    }

    voter_count = get_int("Number of voters: ");
    if (voter_count > MAX_VOTERS)
    {
        printf("Maximum number of voters is %i\n", MAX_VOTERS);
        return 3;
    }

    // Keep querying for votes
    for (int i = 0; i < voter_count; i++)
    {

        // Query for each rank
        for (int j = 0; j < candidate_count; j++)
        {
            string name = get_string("Rank %i: ", j + 1);

            // Record vote, unless it's invalid
            if (!vote(i, j, name))
            {
                printf("Invalid vote.\n");
                return 4;
            }
        }

        printf("\n");
    }

    // Keep holding runoffs until winner exists
    while (true)
    {
        // Calculate votes given remaining candidates
        tabulate();

        // Check if election has been won
        bool won = print_winner();
        if (won)
        {
            break;
        }

        // Eliminate last-place candidates
        int min = find_min();
        bool tie = is_tie(min);

        // If tie, everyone wins
        if (tie)
        {
            for (int i = 0; i < candidate_count; i++)
            {
                if (!candidates[i].eliminated)
                {
                    printf("%s\n", candidates[i].name);
                }
            }
            break;
        }

        // Eliminate anyone with minimum number of votes
        eliminate(min);

        // Reset vote counts back to zero
        for (int i = 0; i < candidate_count; i++)
        {
            candidates[i].votes = 0;
        }
    }
    return 0;
}

// Record preference if vote is valid
bool vote(int voter, int rank, string name)
{
    for (int i = 0; i < candidate_count; i++)
    {
        if (strcmp(candidates[i].name, name) == 0)
        {
            preferences[voter][rank] = i;
            return true;
        }
    }
    return false;
}

// Tabulate votes for non-eliminated candidates
void tabulate(void)
{
    for (int i = 0; i < voter_count; i++)
    {
        for (int j = 0; j < candidate_count; j++)
        {
            int candidate_index = preferences[i][j];

            if (candidates[candidate_index].eliminated == false)
            {
                candidates[candidate_index].votes++;
                break;
            }
        }
    }
    return;
}

// Print the winner of the election, if there is one
bool print_winner(void)
{
    int majority = voter_count / 2; // 정확히는 이 값 +1
    for (int i = 0; i < candidate_count; i++)
    {
        if (candidates[i].votes > majority && !candidates[i].eliminated)
        {
            printf("%s\n", candidates[i].name);
            return true;
        }
    }
    return false;
}


// Return the minimum number of votes any remaining candidate has
int find_min(void)
{
    int min = MAX_VOTERS;
    for (int i = 0; i < candidate_count; i++)
    {
        if (candidates[i].votes < min && candidates[i].eliminated == false)
        {
            min = candidates[i].votes;
        }
    }
    return min;
}

// Return true if the election is tied between all candidates, false otherwise
bool is_tie(int min)
{
    bool tie = true;
    for (int i = 0; i < candidate_count; i++)
    {
        if (candidates[i].votes > min && candidates[i].eliminated == false)
        {
            tie = false;
        }
    }
    return tie;
}

// Eliminate the candidate (or candidates) in last place
void eliminate(int min)
{
    for (int i = 0; i < candidate_count; i++)
    {
        if (candidates[i].votes == min && candidates[i].eliminated == false)
        {
            candidates[i].eliminated = true;
        }
    }
    return;
}

```

`candidates[i].eliminated == false` 쓰는 몇 부분은 지워도 될거같긴 한데, 일단 방어코드 느낌으로 냅둠. 