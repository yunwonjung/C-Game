#include <windows.h>
#include <iostream>
#include <assert.h>
#include <stdio.h>
#include <string.h>
#include <cstdlib>
#include <ctime>
using namespace std;

const int MaxCard=52;
const int CardGap=4;
const int Speed=1000;
const int PromptSpeed=2000;

void gotoxy(int x, int y)
{
    COORD pos = {x-1, y-1};
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), pos);
}


struct SCard
{
     char Name[5];
     SCard() { Name[0]=0; }
     SCard(const char *pName) {
          strcpy(Name,pName);
     }
     int GetNumber() const { 
          if (isdigit(Name[0])) return Name[0]-'0'; 
          if (Name[0]=='T') return 10;
          if (Name[0]=='J') return 11;
          if (Name[0]=='Q') return 12;
          return 13;
     };
     int GetKind() const { 
          if (strcmp(Name+1,"��")==0) return 0;
          else if (strcmp(Name+1,"��")==0) return 1;
          else if (strcmp(Name+1,"��")==0) return 2;
          else return 3;
     }
     friend ostream &operator <<(ostream &c, const SCard &C) { 
          return c << C.Name; 
     }
     bool operator ==(const SCard &Other) const { 
          return (strcmp(Name,Other.Name) == 0);
     }
     bool operator <(const SCard &Other) const {
          if (GetNumber() < Other.GetNumber()) return true;
          if (GetNumber() > Other.GetNumber()) return false;
          if (GetKind() < Other.GetKind()) return true;
          return false;
     }
};
SCard TaroCard[MaxCard]={

     "1��","2��","3��","4��","5��","6��","7��","8��","9��","T��","J��","Q��","K��",

     "1��","2��","3��","4��","5��","6��","7��","8��","9��","T��","J��","Q��","K��",

     "1��","2��","3��","4��","5��","6��","7��","8��","9��","T��","J��","Q��","K��",

     "1��","2��","3��","4��","5��","6��","7��","8��","9��","T��","J��","Q��","K��",

};

class CCardSet
{
protected:
     SCard Card[MaxCard];
     int Num;
     const int sx,sy;
     CCardSet(int asx,int asy) : sx(asx), sy(asy) { Num=0; }
 
public:
     int GetNum() { return Num; }
     SCard GetCard(int idx) { return Card[idx]; }
     void Reset() { 
          for (int i=0;i<MaxCard;i++) Card[i].Name[0]=0;
          Num=0; 
     }
     void InsertCard(SCard C);
     SCard RemoveCard(int idx);
     int FindSameCard(SCard C,int *pSame);
     int FindFirstCard(const char *pName);
};
 
void CCardSet::InsertCard(SCard C) {
     int i;
 
     if (C.Name[0] == 0) return;
     for (i=0;i<Num;i++) {
          if (C < Card[i]) break;
     }
     memmove(&Card[i+1],&Card[i],sizeof(SCard)*(Num-i));
     Card[i]=C;
     Num++;
}
 
SCard CCardSet::RemoveCard(int idx) {
     assert(idx < Num);
     SCard C=Card[idx];
     memmove(&Card[idx],&Card[idx+1],sizeof(SCard)*(Num-idx-1));
     Num--;
     return C;
}
 
int CCardSet::FindSameCard(SCard C,int *pSame) {
     int i,num;
     int *p=pSame;
 
     for (i=0,num=0;i<Num;i++) {
          if (Card[i].GetNumber() == C.GetNumber()) {
              num++;
              *p++=i;
          }
     }
     *p=-1;
     return num;
}
 
int CCardSet::FindFirstCard(const char *pName) {
     int i;
 
     for (i=0;i<Num;i++) {
          if (strstr(Card[i].Name,pName) != NULL) {
              return i;
          }
     }
     return -1;
}

class CDeck : public CCardSet
{
public:
     CDeck(int asx,int asy) : CCardSet(asx,asy) { ; }
     void Shuffle() {
          int i,n;
          for (i=0;i<MaxCard;i++) {
              do {
                   n=rand()%52;
              } while (Card[n].Name[0] != NULL);
              Card[n]=TaroCard[i];
              Num++;
          }
     }
     SCard Pop() { return RemoveCard(Num-1); }
     bool IsEmpty() { return Num==0; }
     bool IsNotLast() { return Num > 1; }
     void Draw(bool bFlip) {
          gotoxy(sx,sy);
          cout << "??? " << (bFlip ? Card[Num-1].Name:"   ");
     }
};

CDeck Deck(18,9);
int main() {
	srand ((unsigned int)time(0));
	for(int i=0;i<52;i++) {
	if(i%13==0) cout << "\n";
    cout <<  TaroCard[rand()%52];
}
Deck.Draw(false);

    return 0;
}
