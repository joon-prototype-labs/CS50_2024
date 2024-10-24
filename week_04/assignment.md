### References

- https://cs50.harvard.edu/x/2024/psets/4/
- https://manual.cs50.io/

### Note

 ```c 
volume.c
 
// Modifies the volume of an audio file

#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

typedef uint8_t BYTE;
typedef int16_t SAMPLE;

// Number of bytes in .wav header
const int HEADER_SIZE = 44;

int main(int argc, char *argv[])
{
    // Check command-line arguments
    if (argc != 4)
    {
        printf("Usage: ./volume input.wav output.wav factor\n");
        return 1;
    }

    // Open files and determine scaling factor
    FILE *input = fopen(argv[1], "r");
    if (input == NULL)
    {
        printf("Could not open file.\n");
        return 1;
    }

    FILE *output = fopen(argv[2], "w");
    if (output == NULL)
    {
        printf("Could not open file.\n");
        return 1;
    }

    float factor = atof(argv[3]);

    // fread와 fwrite는 파일의 현재 위치(file cursor/pointer)를 기준으로 데이터를 읽거나 쓴다.
    // 또한, 파일을 읽거나 쓸수록 파일 위치가 자동으로 이동한다.
    // 따라서 따로 파일을 읽거나 쓸 위치 지정을 안해도 자연스럽게 동작하는 것.

    BYTE header[HEADER_SIZE];
    fread(&header, sizeof(BYTE), HEADER_SIZE, input);
    fwrite(&header, sizeof(BYTE), HEADER_SIZE, output);

    SAMPLE sample;
    while (fread(&sample, sizeof(SAMPLE), 1, input) != 0)
    {
        sample *= factor;
        fwrite(&sample, sizeof(SAMPLE), 1, output);
    }

    // Close files
    fclose(input);
    fclose(output);
}
 ```

 ```c
helpers.c

#include "helpers.h"
#include <math.h>
#include <stdio.h>
#include <stdlib.h>

BYTE coerce_at_most_255(double x);

// 확실히 이런식으로 복잡한 요구사항이 미리 정리되어 있으므로 구현하기 쉬워서 좋다.
// filter 보면서 C 사용하는 법 공부도 좀 됨.
// 생각 외로 어려웠던게, kotlin같은 언어에서 제공하는 고수준 기능을 직접 구현해야 하는거랑.
// 변수 타입 생각하면서 코드 짜는거?
// 최근에 코틀린으로 val 남발해서 그런 것도 있고, 애매하면 Long 같은거 써버리니까...
// 오버/언더플로우 걱정할 일이 거의 없었음.

// Convert image to grayscale
void grayscale(int height, int width, RGBTRIPLE image[height][width])
{
    for (int h = 0; h < height; h++)
    {
        for (int w = 0; w < width; w++)
        {
            // 포인터로 설정 안하면, 값 복사되서 수정 안됨. 주의하기.
            RGBTRIPLE *cur_rgb = &image[h][w];
            double sum_rgb = (cur_rgb->rgbtBlue + cur_rgb->rgbtGreen + cur_rgb->rgbtRed);
            BYTE avg_rgb = round(sum_rgb / 3);
            cur_rgb->rgbtBlue = avg_rgb;
            cur_rgb->rgbtGreen = avg_rgb;
            cur_rgb->rgbtRed = avg_rgb;
        }
    }
    return;
}

// Reflect image horizontally
void reflect(int height, int width, RGBTRIPLE image[height][width])
{
    for (int h = 0; h < height; h++)
    {
        int front_idx = 0;
        int rear_idx = width - 1;
        while (front_idx <= rear_idx)
        {
            // 포인터로 설정 안하면, 값 복사되서 수정 안됨. 주의하기.
            RGBTRIPLE temp;
            RGBTRIPLE *front_rgb = &image[h][front_idx];
            RGBTRIPLE *rear_rgb = &image[h][rear_idx];

            // 이런 코드가 다루는게 포인터 개념 이해하기 좋은듯.
            // 역참조(*)가 붙으면, 해당 포인터가 바라보는 원본을 다루겠다는 의미.
            temp = *front_rgb;
            *front_rgb = *rear_rgb;
            *rear_rgb = temp;

            ++front_idx;
            --rear_idx;
        }
    }
    return;
}

// Blur image
void blur(int height, int width, RGBTRIPLE image[height][width])
{
    // 이거 좀 고민되는게, 원본을 수정하면 관련된 다른 애들의 값을 못 구하고, 그렇다고 TEMP 처럼
    // 저장하자니 2배인데 흠... 뭐 어쩔수 없이 메모리 할당해야하나? 근데 강의에서 따로 말 안한거
    // 보면 다른 방법이 있을거 같은데...? 내 생각엔 강의에서 메모리 할당 나오는거 보면 쓰는게 맞는거
    // 같음. 사실 스택에 해도 상관없긴 한데, 그럼 메모리 낭비가 심하니까...

    RGBTRIPLE(*temp)[width] = calloc(height, width * sizeof(RGBTRIPLE));

    // 블러 처리
    for (int h = 0; h < height; h++)
    {
        for (int w = 0; w < width; w++)
        {
            double sum_r = 0, sum_g = 0, sum_b = 0;
            int count = 0;

            // d: direction
            for (int dh = -1; dh <= 1; dh++)
            {
                for (int dw = -1; dw <= 1; dw++)
                {
                    // n: new
                    int nh = h + dh;
                    int nw = w + dw;

                    if (nh >= 0 && nh < height && nw >= 0 && nw < width)
                    {
                        // 배열은 포인터 없어도 값 가져옴. - 배열이 내부적으론 포인터의 다른 형태?
                        // 정도로 볼 수 있으니까
                        sum_r += image[nh][nw].rgbtRed;
                        sum_g += image[nh][nw].rgbtGreen;
                        sum_b += image[nh][nw].rgbtBlue;
                        ++count;
                    }
                }
            }
            temp[h][w].rgbtRed = round(sum_r / count);
            temp[h][w].rgbtGreen = round(sum_g / count);
            temp[h][w].rgbtBlue = round(sum_b / count);
        }
    }

    // 값 복사
    for (int h = 0; h < height; h++)
    {
        for (int w = 0; w < width; w++)
        {
            image[h][w] = temp[h][w];
        }
    }
    return;
}

// Detect edges
// 여긴 코드 구현보다, 요구사항이 이해가 안가서 GPT한테 설명해달라고 여러번 물어봄.
// 코드 자체는 안물어봤으니 괜찮지 않을까...?
void edges(int height, int width, RGBTRIPLE image[height][width])
{
    const int gx[3][3] = {{-1, 0, 1}, {-2, 0, 2}, {-1, 0, 1}};

    const int gy[3][3] = {{1, 2, 1}, {0, 0, 0}, {-1, -2, -1}};

    const RGBTRIPLE BLACK = {0, 0, 0};

    RGBTRIPLE(*temp)[width] = calloc(height, width * sizeof(RGBTRIPLE));

    for (int h = 0; h < height; h++)
    {
        for (int w = 0; w < width; w++)
        {
            double sum_gx_value_r = 0, sum_gx_value_g = 0, sum_gx_value_b = 0;
            double sum_gy_value_r = 0, sum_gy_value_g = 0, sum_gy_value_b = 0;

            // d: direction
            for (int dh = -1; dh <= 1; dh++)
            {
                for (int dw = -1; dw <= 1; dw++)
                {
                    // n: new
                    int nh = h + dh;
                    int nw = w + dw;

                    // k: kernel
                    int kh = dh + 1;
                    int kw = dw + 1;

                    if (nh >= 0 && nh < height && nw >= 0 && nw < width)
                    {
                        sum_gx_value_r += image[nh][nw].rgbtRed * gx[kh][kw];
                        sum_gx_value_g += image[nh][nw].rgbtGreen * gx[kh][kw];
                        sum_gx_value_b += image[nh][nw].rgbtBlue * gx[kh][kw];
                        sum_gy_value_r += image[nh][nw].rgbtRed * gy[kh][kw];
                        sum_gy_value_g += image[nh][nw].rgbtGreen * gy[kh][kw];
                        sum_gy_value_b += image[nh][nw].rgbtBlue * gy[kh][kw];
                    }
                    else
                    {
                        sum_gx_value_r += BLACK.rgbtRed * gx[kh][kw];
                        sum_gx_value_g += BLACK.rgbtGreen * gx[kh][kw];
                        sum_gx_value_b += BLACK.rgbtBlue * gx[kh][kw];
                        sum_gy_value_r += BLACK.rgbtRed * gy[kh][kw];
                        sum_gy_value_g += BLACK.rgbtGreen * gy[kh][kw];
                        sum_gy_value_b += BLACK.rgbtBlue * gy[kh][kw];
                    }
                }
            }
            BYTE value_r =
                coerce_at_most_255(round(sqrt(pow(sum_gx_value_r, 2) + pow(sum_gy_value_r, 2))));
            BYTE value_g =
                coerce_at_most_255(round(sqrt(pow(sum_gx_value_g, 2) + pow(sum_gy_value_g, 2))));
            BYTE value_b =
                coerce_at_most_255(round(sqrt(pow(sum_gx_value_b, 2) + pow(sum_gy_value_b, 2))));

            temp[h][w].rgbtRed = value_r;
            temp[h][w].rgbtGreen = value_g;
            temp[h][w].rgbtBlue = value_b;
        }
    }

    for (int h = 0; h < height; h++)
    {
        for (int w = 0; w < width; w++)
        {
            image[h][w] = temp[h][w];
        }
    }
    return;
}

// 내부 구현적인거보다 "byte 오버플로우 방지" 같은 네이밍이 더 좋을지도?
BYTE coerce_at_most_255(double x)
{
    if (x > 255)
    {
        return 255;
    }
    return x;
}
 ```


 ```c 
recover.c
 
#include <stdbool.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

char *set_filename(char suffix_number, char arr[8]);

// 뭔가 강의에서 한거처럼 깔끔하지 않은데...
int main(int argc, char *argv[])
{
    if (argc != 2)
    {
        printf("Usage: ./recover FILE\n");
        return 1;
    }

    char suffix_number = 0;

    FILE *card = fopen(argv[1], "r");

    uint8_t buffer[512];

    FILE *file = NULL;
    char filename[] = {'0', '0', '0', '.', 'j', 'p', 'g', '\0'};
    while (fread(buffer, 1, 512, card) == 512)
    {
        bool signature = buffer[0] == 0xff && buffer[1] == 0xd8 && buffer[2] == 0xff &&
                         buffer[3] >= 0xE0 && buffer[3] <= 0xEF;

        if (signature)
        {
            if (file != NULL)
            {
                fclose(file);
            }

            set_filename(suffix_number, filename);
            ++suffix_number;
            file = fopen(filename, "w");
        }

        if (file == NULL)
        {
            continue;
        }

        fwrite(buffer, 1, 512, file);
    }
    if (file != NULL)
    {
        fclose(file);
    }
    fclose(card);
}

char *set_filename(char suffix_number, char arr[8])
{
    if (suffix_number < 10)
    {
        arr[2] = '0' + suffix_number;
    }
    else if (suffix_number >= 10 && suffix_number < 100)
    {
        arr[1] = '0' + suffix_number / 10;
        arr[2] = '0' + suffix_number % 10;
    }
    else
    {
        arr[0] = '0' + suffix_number / 100;
        arr[1] = '0' + suffix_number / 10;
        arr[2] = '0' + suffix_number % 10;
    }
    return arr;
}
 ```
