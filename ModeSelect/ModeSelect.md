# 모드 선택창 코드 설명서

2024.05.01 이호승

<br/>
<br/>
<br/>
기능 : 여러 모드를 선택할 수 있는 모드 선택창을 만듬  
이용 방법 : 모드 선택 - 화살표키 위아래, 모드 확정 - 스페이스바

## 전체적인 코드 구조 

메인 함수의 title()함수가 실행된 후 모드 선택창으로서의 title2()를 실행한다. 

먼저 변수와 함수를 선언한다.
화면을 클리어한다. 
그리고 키 입력 확인, 화면 출력을 반복하는 for문을 실행한다. 

키 입력은 이미 있는 코드와 비슷하게 사용하였다.
화면 출력에서 모션을 넣기 위해 for문의 cnt(count)변수를 사용하여 0.3초마다 바뀌도록 했다. 

## 코드 분석

```C
void title2(void)
{
    // reset
    void gop(int px, int py, char* txt); // 코드를 단순화하기 위한 함수
    int x = 5, y = 4; // 기준점 위치
    int mode = 0, mode_temp = -1; // 모드 변수, 이전 모드를 기억하는 변수
    int game_start = 0;
    system("cls"); // 화면 초기화
    gop(x+10-5, y+0, ":: Select the mode ::");


    int cnt;
    for (cnt = 0;; cnt++) {
        // key_input
        if (_kbhit())
        {
            do { key = _getch(); } while (key == 224);
            switch (key) {
            case UP:
                if(mode>0) mode += -1;
                break;
            case DOWN:
                if(mode<1) mode += 1;
                break;
            case SPACE:
                game_start = 1;
                break;
            }
        }
        if(game_start) break; // 게임 시작(모드 선택창 함수 종료)

		// 모드가 바뀔 때마다 화면 초기화
        if(mode_temp != mode) {
            gop(x+10-4, y+4, "    mode0    ");
            gop(x+10-4, y+6, "    mode1    ");
            mode_temp = mode;
			cnt = 0;
        }

        if(cnt % 60 == 0) // 0.6초 모듈러 연산으로 0초일 때
        {
            gotoxy(x+10-4, y+4+2*mode); 
            printf(">>  mode%d  <<", mode); 
        }
        if(cnt % 60 == 30) // 0.6초 모듈러 연산으로 0.3초일 때
        {
            gotoxy(x+10-4, y+4+2*mode);
            printf(" >> mode%d << ", mode); 
        }

        Sleep(10); // 0.1초 딜레이 
    }
}

void gop(int px, int py, char* txt)
{
    gotoxy(px, py);
    printf("%s", txt);
}
```

## 코드를 적용하는 법

원본 코드의 상단에 title2함수의 선언을 하고 main함수에서 title()바로 아래에 title2()를 입력하면 된다. 

ex) main함수의 new 부분을 보면 된다. 

```C
void title2(void);
```

```C
int main() {
    int i;

    srand((unsigned)time(NULL)); //난수표생성 
    setcursortype(NOCURSOR); //커서 없앰 
    title(); //메인타이틀 호출 
	// ---------------new----------------
    title2(); // 모드 선택창 실행
	// ----------------------------------

    reset(); //게임판 리셋 

    while (1) {
        for (i = 0; i < 5; i++) { //블록이 한칸떨어지는동안 5번 키입력받을 수 있음 
            check_key(); //키입력확인 
            draw_main(); //화면을 그림 
            Sleep(speed); //게임속도조절 
            if (crush_on && check_crush(bx, by + 1, b_rotation) == false) Sleep(100);
            //블록이 충돌중인경우 추가로 이동및 회전할 시간을 갖음 
            if (space_key_on == 1) { //스페이스바를 누른경우(hard drop) 추가로 이동및 회전할수 없음 break; 
                space_key_on = 0;
                break;
            }
        }
        drop_block(); // 블록을 한칸 내림 
        check_level_up(); // 레벨업을 체크 
        check_game_over(); //게임오버를 체크 
        if (new_block_on == 1) new_block(); // 뉴 블럭 flag가 있는 경우 새로운 블럭 생성 
    }
}
```