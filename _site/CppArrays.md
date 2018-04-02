## passing arrays
``` c++
#include <iostream>
#include <vector>
#include <deque>
#include <list>

// Method 1: works but very little security.  It is impossible to validate
// the inputs since the size of data still cannot be validated. If length is too large
// undefined behavior will occur.
void printArray(int data[], int length)
{
    for(int i(0); i < length; ++i)
    {
        std::cout << data[i] << ' ';
    }
    std::cout << std::endl;
}

// Method 2: Type safe and more generic.  Works with any container that supports forward iterators.
// Limitation - cannot validate iterators so caller could pass null or invalid pointers.  Typesafe - won't
// allow you to pass inconsistent iterator types.  Allows you to pass any valid range of a container.
template <class ForwardIteratorType> 
void printArray(ForwardIteratorType begin, ForwardIteratorType end)
{
    while(begin != end)
    {
        std::cout << *begin << ' ';
        ++begin;
    }
    std::cout << std::endl;
}

// Method 3 - This implementation is as typesafe and secure as you can get but
// does not allow a subrange since the entire container is expected.  It could
// be useful if you want that extra security and know that you want to operate
// on the entire container.
template <class ContainerType> 
void printArray(const ContainerType& container)
{
    ContainerType::const_iterator current(container.begin()), end(container.end());
    for( ; 
        current != end; 
        ++current)
    {
        std::cout << *current << ' ';
    }
    std::cout << std::endl;
}

int main()
{
    // Method 1.
    const int LENGTH(6);
    int data[LENGTH] = { 5, 7, 8, 9, 1, 2 };
    printArray(data, LENGTH);

    // Method 2.
    printArray(data, data + LENGTH);
    std::vector<int> vData(data, data + LENGTH);
    printArray(vData.begin(), vData.end());
    std::list<int> lData(data, data + LENGTH);
    printArray(lData.begin(), lData.end());
    std::deque<int> dData(data, data + LENGTH);
    printArray(dData.begin(), dData.end());
    // won't compile if caller accidentally mixes iterator types.
    //printArray(dData.begin(), vData.end());

    // method 3.
    printArray(vData);
    printArray(dData);
    printArray(lData);
	return 0;
}
```

## Returning arrays from functions
```
#include <vector>
#include <iostream>
#include <limits>

// Prints out array elements. Method 2 from PART I.
template <class ForwardIteratorType> 
void printArray(ForwardIteratorType begin, ForwardIteratorType end)
{
    while(begin != end)
    {
        std::cout << *begin << ' ';
        ++begin;
    }
    std::cout << std::endl;
}

// The caller must decide whether to pass an empty container.  This function will 
// add to it.  
void readScores(std::vector<int>& container)
{
    std::cout << "Type the list of scores followed by a non-numeric character and press enter when finished. " 
              << "For instance (22 25 26 f <enter> " << std::endl;
    int temp(0);
    while(std::cin >> temp)
    {
        container.push_back(temp);
    }
    // clear and discard any leftover data from the input stream.
    std::cin.clear();
    std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
}

int main()
{
    std::vector<int> scores; // uninitialized.  Let readScores fill it.
    readScores(scores);
    printArray(scores.begin(), scores.end());
    return 0;
}
```
