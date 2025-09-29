```c++
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

class LineCounter {
  public:
   static int count(const char* fileName); 
  private:
   static int nLines;
   static ifstream file;
  
   static void initialize(const char* fileName);
   static void countTheLines();
   static int finalize();
};

int LineCounter::nLines;

ifstream LineCounter::file;

void LineCounter::initialize(const char* fileName) {
  nLines = 0;
  file.open(fileName);
}

void LineCounter::countTheLines() {
  string line;
  while (getline (file, line))
    nLines++;
}

int LineCounter::finalize() {
  file.close();
  return nLines; 
}

int LineCounter::count(const char* fileName) {
  initialize(fileName);
  countTheLines();
  return finalize();
}
```
