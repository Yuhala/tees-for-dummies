CXX = g++ 
CC = gcc
OCCLUM_GCC = occlum-gcc


.PHONE = all clean

all: occlum-hello


occlum-cpc: helloworld.c
	$(OCCLUM_GCC) -Wall -o $@ $^ 


clean:
	rm -f occlum-hello helloworld.o
