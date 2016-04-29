#include "main.h"
using namespace std;

int main (int argc, char *argv[])
{
	stringText = (unsigned char*)"000102030405060708090A0B0C0D0E0F101112131415161718191A1B1C1D1E1F202122232425262728292A2B2C2D2E2F303132333435363738393A3B3C3D3E3F";
	messageLength = (strlen((char*)stringText));
	messageStart = new byte[messageLength];

	if(test == true) {
		for(int i=0; i < messageLength; i+=2) {
			messageStart[i/2] = stringToInt(stringText[i], stringText[i+1]);
		}
		cout << "message start=" << endl;
		for(int i=0; i < messageLength/2; i++) {
			cout << hex << (int)messageStart[i] << "  ";
		}
		cout << endl;
	}
	else {
		for(int i=0; i < messageLength; i+=2) {
				messageStart[i] = stringText[i];
		}
	}

	addition();			//Дополняем сообщение
	cout << hex << "messageAddition:" << endl;
	for(int i=0; i<addMessageLength; i++) cout << (int)messageAddition[i] << " ";
	cout << endl;
	numberBlocks = (int)(addMessageLength/64);

	for(currentBlock = 0; currentBlock < numberBlocks; currentBlock++) {
		cout << endl;
		textInBlock();

		iteration = 0;
		runTranfsormK();
		iteration = 0;
		runTranfsormN();
		addArray(afterTransformK, afterTransformN);

		cout << "end block" << currentBlock << ":" << endl;
		if(currentBlock == 0) {
			resultAdd[0][0] ^= 0x40;
			for(int i=0; i<8; i++) {
				for(int j=0; j<8; j++) {
					endHash[i][j] = resultAdd[i][j];
					cout << hex << (int)endHash[i][j] << "  ";
				}
				cout << endl;
			}
		}
		else {
			addArray(resultAdd, endHash);
			for(int i=0; i<8; i++) {
				for(int j=0; j<8; j++) {
					endHash[i][j] = resultAdd[i][j];
					cout << hex << (int)endHash[i][j] << "  ";
				}
				cout << endl;
			}
		}
	}
	return 0;
}

void addition() {
	messageLength /= 2;
	d = 64 - ((messageLength + 13) % 64);
	addMessageLength = messageLength + d + 13;//12;
	messageAddition = new byte[addMessageLength];

	int point = 0;		//Номер позиции на данный момент в конечном массиве
	for(; point<messageLength; point++) {		//Записываем в начало массива начальное не преобразованное сообщение
		messageAddition[point] = messageStart[point];  //Записываем начальное сообщение
	}
	messageAddition[point] = 0x80;		//Записываем 1 элемент d
	point++;
	for(; point<(messageLength+d); point++) {		//Записываем цифру 0 в массив d раз
		messageAddition[point] = 0;		//Дописываем нужное количество нолей
	}
	for(int len = (messageLength*8); point < addMessageLength; point++ ){ 		//Записываем в последние 12 байт число N
		messageAddition[point] = len%256;
		len = (int) (len / 256);
		if(len < 256) {
			point++;
			messageAddition[point] = len;
			point++;
			while(point < addMessageLength) {
				messageAddition[point] = 0;
				point++;
			}
			break;
		}
	}
}

void runTranfsormK() {
	if(iteration == 0 && currentBlock == 0) {
		for(int i=0; i<8; i++) {
			for (int j=0; j<8; j++) {
				textTransform[i][j] = textNorm[i][j];
			}
		}
		textTransform[0][0] ^= 0x40;
	}
	else if(iteration == 0 && currentBlock != 0) {
		for(int i=0; i<8; i++) {
			for (int j=0; j<8; j++) {
				textTransform[i][j] = endHash[i][j] ^ textNorm[i][j];
			}
		}
	}

	addConstIteration_k_(textTransform, iteration);
	replace(ciK);
	shift();
	linearTransformation();

	iteration++;
	if(iteration >= 10) { 
		for(int i=0; i<8; i++) {
			for (int j=0; j<8; j++) {
				afterTransformK[i][j] = textTransform[i][j];
			}
		}
		return;
	}
	else runTranfsormK();		//Рекурсия
}

void runTranfsormN() {
	if(iteration == 0) {
		for(int i=0; i<8; i++) {
			for (int j=0; j<8; j++) {
				textTransform[i][j] = textNorm[i][j];
			}
		}
	}

	addConstIteration_n_(textTransform, iteration);
	replace(ciN);
	shift();
	linearTransformation();

	iteration++;
	if(iteration >= 10){
		for(int i=0; i<8; i++) {
			for (int j=0; j<8; j++) {
				afterTransformN[i][j] = textTransform[i][j];
			}
		}
		return;
	}
	else runTranfsormN();		//Рекурсия
}

void transpon(){
	for(int i=0; i<8; i++){
		for(int j=0; j<8; j++){
			textNorm[i][j] = textNorm_1[j][i];
		}
	}
}

void addConstIteration_k_(unsigned char m[][8],int v) {
	for(int i=0; i<8; i++) {
		ciK[0][i] = m[0][i] ^ ((i << 4)^v);
		for(int j=1; j<8; j++) {
			ciK[j][i] = m[j][i];
		}
	}
}
void addConstIteration_n_(unsigned char m[][8], int v) {
	for(int i=0; i<8; i++ ) {
		ciN[0][i] = 0xF3;
		for(int j=1; j<7; j++) {
			ciN[j][i] = 0xF0;
		}
		ciN[7][i] = ((7-i)<<4)^v;
	}
	int buf = 0;
	for(int i=0; i<8; i++ ) {
		buf = 0;
		for(int j=0; j<8; j++) {
			buf += (int)(ciN[j][i]) + (int)(m[j][i]);
			ciN[j][i] = buf;
			buf /= 256;
		}
	}
}

void replace(unsigned char arr[][8]) {
	for(int i=0; i<8; i++ ) {
		for(int j=0; j<8; j++) {
			textReplace[i][j] = Pi[i%4][arr[i][j]/16][arr[i][j]%16];
		}
	}
}
void shift() {
	for(int i=0; i<8; i++ ) {
		for(int j=0; j<8; j++) {
			textShift[i][(j+i)%8] = textReplace[i][j];
		}
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
	for(int i=0; i<8; i++ ) {
		for(int j=0; j<8; j++) {
			textTransform[i][j] = 0;
		}
	}

	for(int i=0; i<8; i++) {
		for(int h=0; h<8; h++ )    v_AfterShift[(h+i)%8] = v[h];		//циклический сдвиг V на i

		for(int j=0; j<8; j++){
			for(int h=0; h<8; h++) { 		
				column[h] = textShift[h][j];		//записываем колонку в массив
				textTransform[i][j] ^= mult(column[h], v_AfterShift[h]); 	//вызов функции mult
			}
		}
	}
}

void addArray(byte arr1[8][8], byte arr2[8][8]) {
	for(int i=0; i<8; i++) {
		for(int j=0; j<8; j++) {
			resultAdd[i][j] = arr1[i][j] ^ arr2[i][j];
		}
	}
}

void printArr(byte arr[8][8]) {
	for(int i=0; i<8; i++) {
		for(int j=0; j<8; j++) {
			cout << hex << (int)(arr[i][j]) << "  ";
		}
		cout << endl;
	}
}

int stringToInt(unsigned char symb1, unsigned char symb2) {
	if(symb1 == '0') return getIntFromChar(symb2);
	else {
		return (getIntFromChar(symb1) * 16 + getIntFromChar(symb2));
	}
}

int getIntFromChar(unsigned char symbol) {
	switch(symbol) {
		case '0':
			return 0;
		case '1':
			return 1;
		case '2':
			return 2;
		case '3':
			return 3;
		case '4':
			return 4;
		case '5':
			return 5;
		case '6':
			return 6;
		case '7':
			return 7;
		case '8':
			return 8;
		case '9':
			return 9;
		case 'A':
			return 10;
		case 'B':
			return 11;
		case 'C':
			return 12;
		case 'D':
			return 13;
		case 'E':
			return 14;
		case 'F':
			return 15;
	default:
		return 0;
	}
}

void textInBlock() {
	int i = currentBlock * 64;
	textNorm[i%8][(int)(i/8)] = messageAddition[i];
	i++;
	for(; i < addMessageLength && (i % 64) != 0; i++) {
		textNorm[i%8][(int)(i/8)] = messageAddition[i];
	}
}
