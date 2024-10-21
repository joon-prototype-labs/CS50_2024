### References

- https://cs50.harvard.edu/x/2024/psets/2/
- https://manual.cs50.io/

### Note

이번에도 난이도 선택 가능하면 높은 걸로 선택함.

확실히 walkthorgh를 봐야 하는게 맞았다. 애매한 글자 수나 문장 단어 기준 같은 것들을 이야기해주니까.      
요구사항을 명확하게 제시해주고 그걸 코드로 옮기는 거만 하면 되서 난이도가 많이 쉬워짐.


```c
scrabble.c

#include <cs50.h>
#include <ctype.h>
#include <stdio.h>
#include <string.h>

const int POINTS[] = {1, 3, 3, 2,  1, 4, 2, 4, 1, 8, 5, 1, 3,
                      1, 1, 3, 10, 1, 1, 1, 1, 4, 4, 8, 4, 10};

int coumpute_score(string word);

int main(void)
{
    string p1_word = get_string("Player 1: ");
    string p2_word = get_string("Player 2: ");

    int p1_score = coumpute_score(p1_word);
    int p2_score = coumpute_score(p2_word);

    if (p1_score < p2_score)
    {
        printf("Player 2 wins!");
    }
    else if (p1_score > p2_score)
    {
        printf("Player 1 wins!");
    }
    else
    {
        printf("Tie!");
    }
}

int coumpute_score(string word)
{
    int score = 0;

    for (int i = 0, len = strlen(word); i < len; i++)
    {
        char upper_word = toupper(word[i]);
        score += POINTS[upper_word - 'A'];
    }
    return score;
}
```


```c 

readability.c

#include <cs50.h>
#include <ctype.h>
#include <math.h>
#include <stdio.h>
#include <string.h>

int count_letters(string text);
int count_words(string text);
int count_sentences(string text);

// 조건 정리
// ! . ? 가 문장의 끝 = ! . ? 개수가 문장의 개수
// 공백이 단어를 구분하는 단위 = 문장이 끝나는 마지막 단어는 공백이 없으므로, 문장의 (공백 + 1)이
// 해당 문장의 단어의 수 대소문자 알파벳이 단어, 기호나 공백은 제외됨. 
// 공식 결과에 반올림하여 학년 수준을 구할 수 있음.

int main(void)
{
    string text = get_string("Text: ");

    int letters = count_letters(text);
    int words = count_words(text);
    int sentences = count_sentences(text);

    double l = (double) letters / (double) words * 100.0;
    double s = (double) sentences / (double) words * 100.0;
    double index = 0.0588 * l - 0.296 * s - 15.8;
    int grade = (int) round(index);

    printf("%i, %i, %i, %f, %f, %f, %i", letters, words, sentences, l, s, index, grade);

    if (grade < 1)
    {
        printf("Before Grade 1\n");
    }
    else if (grade < 16)
    {
        printf("Grade %i\n", grade);
    }
    else
    {
        printf("Grade 16+\n");
    }
}

int count_letters(string text)
{
    int count = 0;
    for (int i = 0, len = strlen(text); i < len; ++i)
    {
        char cur_text = text[i];
        if (isalpha(cur_text))
        {
            ++count;
        }
    }
    return count;
}

int count_words(string text)
{
    int count = 0;
    for (int i = 0, len = strlen(text); i < len; ++i)
    {
        char cur_text = text[i];
        if (isspace(cur_text))
        {
            ++count;
        }
    }
    return count + 1;
}

int count_sentences(string text)
{
    int count = 0;
    for (int i = 0, len = strlen(text); i < len; ++i)
    {
        char cur_text = text[i];
        // ispunct는 "'"가 포함되는 문제가 있음.
        if (cur_text == '.' || cur_text == '?' || cur_text == '!')
        {
            ++count;
        }
    }
    return count;
}
```


```c
substitution.c


#include <cs50.h>
#include <ctype.h>
#include <stdio.h>
#include <string.h>

int is_alpha(string cipher_text);
int is_duplicate(string cipher_text);
void encrypte(string cipher_text, string plain_text);

// 더 최적화 가능한데 귀찮아서 안함. arr이나 encrypte

int main(int argc, string argv[])
{
    if (argc != 2)
    {
        printf("Usage: ./substitution key\n");
        return 1;
    }

    string cipher_text = argv[1];

    if (is_alpha(cipher_text) == 0 || is_duplicate(cipher_text) == 1)
    {
        printf("Usage: ./substitution key\n");
        return 1;
    }
    else if (strlen(cipher_text) != 26)
    {
        printf("Key must contain 26 characters.\n");
        return 1;
    }

    string plain_text = get_string("plaintext: ");

    encrypte(cipher_text, plain_text);

    return 0;
}

int is_alpha(string cipher_text)
{
    int is_vaild = 1;

    for (int i = 0, len = strlen(cipher_text); i < len; i++)
    {
        if (!isalpha(cipher_text[i]))
        {
            is_vaild = 0;
        }
    }
    return is_vaild;
}

void encrypte(string cipher_text, string plain_text)
{
    printf("ciphertext: ");
    for (int i = 0, len = strlen(plain_text); i < len; i++)
    {
        char cur_char = plain_text[i];
        if (isalpha(cur_char))
        {
            int idx;
            if (isupper(cur_char))
            {
                idx = cur_char - 65;
                printf("%c", toupper(cipher_text[idx]));
            }
            else
            {
                idx = cur_char - 97;
                printf("%c", tolower(cipher_text[idx]));
            }
        }
        else
        {
            printf("%c", cur_char);
        }
    }
    printf("\n");
    return;
}

int is_duplicate(string cipher_text)
{
    int arr[100] = {0};
    int is_duplicate = 0;

    for (int i = 0, len = strlen(cipher_text); i < len; i++)
    {
        if (arr[cipher_text[i] - 65] == 1)
        {
            is_duplicate = 1;
        }
        arr[cipher_text[i] - 65] = 1;
    }
    return is_duplicate;
}

```