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

