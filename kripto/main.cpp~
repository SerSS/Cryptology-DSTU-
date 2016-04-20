#include "main.h"
using namespace std;

int main (int argc, char *argv[])
{
	//addition();
	transpon();
	cout << endl;
	addConstIteration_k_(textNorm, 0);
	cout << endl;
	replace();
	cout << endl;
	shift();
	cout << endl;
	linearTransformation();
	cout << endl;
	return 0;
}

void transpon(){
	for(int i=0; i<8; i++){
		for(int j=0; j<8; j++){
			textNorm[i][j] = textNorm1[j][i];
			//cout << hex << (int)textNorm[i][j] << "  ";
		}
		//cout << endl;
	}
}

void addition() {
	cout << "enter your messege" << endl;
	messageStart = new byte[200];
	cin >> messageStart;
	messageLength = strlen((char*)messageStart);
	d = 64 - ((messageLength + 13) % 64);
	addMessageLength = messageLength + d + 12;
	messageAddition = new byte[addMessageLength];

	int point = 0;		//Номер позиции на данный момент в конечном массиве
	for(; point<messageLength; point++) {		//Записываем в начало массива начальное не преобразованное сообщение
		messageAddition[point] = messageStart[point];  //Записываем начальное сообщение
		cout << point << "-1-" << messageAddition[point] << endl;
	}
	messageAddition[point] = 0x80;		//Записываем 1 элемент d
	point++;
	cout << point << "-2-" << (int)messageAddition[point] << endl;
	for(; point<(messageLength+d); point++) {		//Записываем цифру 0 в массив d раз
		messageAddition[point] = 0;		//Дописываем нужное количество нолей
		cout << point << "-2-" << (int)messageAddition[point]  << endl;
	}
	for(int len = messageLength; point < addMessageLength; point++ ){ 		//Записываем в последние 12 байт число N
		messageAddition[point] = len%256;
		len = (int) (len / 256);
		cout << point << "-3-" << (int)messageAddition[point]  << endl;
		if(len < 256) {
			point++;
			while(point < addMessageLength) {
				messageAddition[point] = 0;
				cout << point << "-4-" << (int)messageAddition[point]  << endl;
				point++;
			}
			break;
		}
	}
	cout << messageAddition << endl;
	cout << strlen((char*)messageAddition);
}
void addConstIteration_k_(unsigned char m[][8],int v) {
	for(int i=0; i<8; i++) {
		ciK[0][i] = m[0][i] ^ ((i << 4)^v);
		for(int j=1; j<8; j++) {
			ciK[j][i] = m[j][i];
		}
	}
	for(int i=0; i<8; i++) {
		for(int j=0; j<8; j++) {
			cout << hex << (int)ciK[i][j] << "  ";
		}
		cout << "\n";
	}
}
void addConstIteration_n_(int v) {
	for(int i=0; i<8; i++ ) {
		ciN[i][0] = 0xF3;
		for(int j=1; j<7; j++) {
			ciN[i][j] = 0xF0;
		}
		ciN[i][7] = ((7-i)<<4)^v;
	}
	for(int i=0; i<8; i++ ) {
		for(int j=1; j<8; j++) {
			cout << (int)ciN[i][j] << "  ";
		}
		cout << endl;
	}
}

void replace() {
	for(int i=0; i<8; i++ ) {
		for(int j=0; j<8; j++) {
			textReplace[i][j] = Pi[i%4][ciK[i][j]/16][ciK[i][j]%16];
		}
	}
	for(int i=0; i<8; i++ ) {
		for(int j=0; j<8; j++) {
			cout << hex << (int)textReplace[i][j] << "  ";
		}
		cout << endl;
	}
}
void shift() {
	for(int i=0; i<8; i++ ) {
		for(int j=0; j<8; j++) {
			textShift[i][(j+i)%8] = textReplace[i][j];
		}
	}
	for(int i=0; i<8; i++ ) {
		for(int j=0; j<8; j++) {
			cout << hex << (int)textShift[i][j] << "  ";
		}
		cout << endl;
	}
}



unsigned char modM(unsigned short a){
	byte temp = 0;
	for(int i=14; i>=8; i--) {
		temp = (a>>i)&1;
		if(temp == 1)	a ^= mod<<(i-8);
	}
	temp = a & 0xff;
	return temp;
}

unsigned char mult(byte column, byte shiftV){
	unsigned short temp = 0;
	byte buf = 0;
	for(int i=0; i<8; i++){
		buf = (shiftV >> i) & 1;
		if(buf == 1) 	temp ^= column << i;
	}
	temp = modM(temp); 
	return temp;
}

void linearTransformation() {
	byte column[8];
	for(int i=0; i<8; i++) {
		for(int h=0; h<8; h++ )    v_AfterShift[(h+i)%8] = v[h];		//циклический сдвиг V на i

		for(int j=0; j<8; j++){
			for(int h=0; h<8; h++) { 		
				column[h] = textShift[h][j];		//записываем колонку в массив
				textTransform[i][j] ^= mult(column[h], v_AfterShift[h]); 	//вызов функции mult
			}
			cout << hex << (int)textTransform[i][j] << "  ";
		}
		cout << endl;
	}
}