### C++ 中头文件的优缺点

在编程竞赛中，我们常见一个头文件：

```c++
#include <bits/stdc++.h>
```

　　发现它是部分C++中支持的一个几乎万能的头文件，包含所有的可用到的C++库函数，如<istream>/<ostream>/<stack>/<queue>.

该头文件的详细内容如下：

```c++
// C++ includes used for precompiling -*- C++ -*-

// Copyright (C) 2003-2013 Free Software Foundation, Inc.
//
// This file is part of the GNU ISO C++ Library.  This library is free
// software; you can redistribute it and/or modify it under the
// terms of the GNU General Public License as published by the
// Free Software Foundation; either version 3, or (at your option)
// any later version.

// This library is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.

// Under Section 7 of GPL version 3, you are granted additional
// permissions described in the GCC Runtime Library Exception, version
// 3.1, as published by the Free Software Foundation.

// You should have received a copy of the GNU General Public License and
// a copy of the GCC Runtime Library Exception along with this program;
// see the files COPYING3 and COPYING.RUNTIME respectively.  If not, see
// <Licenses - GNU Project - Free Software Foundation>.

/** @file stdc++.h

 *  This is an implementation file for a precompiled header.
    */

// 17.4.1.2 Headers

// C
#ifndef _GLIBCXX_NO_ASSERT
#include <cassert>
#endif
#include <cctype>
#include <cerrno>
#include <cfloat>
#include <ciso646>
#include <climits>
#include <clocale>
#include <cmath>
#include <csetjmp>
#include <csignal>
#include <cstdarg>
#include <cstddef>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <ctime>

#if __cplusplus >= 201103L
#include <ccomplex>
#include <cfenv>
#include <cinttypes>
#include <cstdalign>
#include <cstdbool>
#include <cstdint>
#include <ctgmath>
#include <cwchar>
#include <cwctype>
#endif

// C++
#include <algorithm>
#include <bitset>
#include <complex>
#include <deque>
#include <exception>
#include <fstream>
#include <functional>
#include <iomanip>
#include <ios>
#include <iosfwd>
#include <iostream>
#include <istream>
#include <iterator>
#include <limits>
#include <list>
#include <locale>
#include <map>
#include <memory>
#include <new>
#include <numeric>
#include <ostream>
#include <queue>
#include <set>
#include <sstream>
#include <stack>
#include <stdexcept>
#include <streambuf>
#include <string>
#include <typeinfo>
#include <utility>
#include <valarray>
#include <vector>

#if __cplusplus >= 201103L
#include <array>
#include <atomic>
#include <chrono>
#include <condition_variable>
#include <forward_list>
#include <future>
#include <initializer_list>
#include <mutex>
#include <random>
#include <ratio>
#include <regex>
#include <scoped_allocator>
#include <system_error>
#include <thread>
#include <tuple>
#include <typeindex>
#include <type_traits>
#include <unordered_map>
#include <unordered_set>
#endif
```

　在编程竞赛中，使用这个头文件是一个好的想法，特别是当你在选择头文件时想减少时间，我们更加专注于找到算法解决问题而不是软件工程。而从软件工程的视角，我们最好最小化包含头文件，如果你包含了一些你可能不会用到的头文件，就会增加不必要的编译时间和程序大小。

 **bits/stdc++的缺点**

- bits/stdc++.h 不是GNU C++库的标准头文件，所以如果你在一些编译器（除了GCC）上编译你的代码，可能会失败，比如MSVC没有这个头文件。
- 使用它会包含很多不必要的东西，并且会增加编译时间
- 这个头文件不是C++标准的一部分，所以是不可移植的，应该尽量避免。
- 尽管标准中有一些通用的头文件，但还是应该避免使用它来代替特定的头文件，因为编译器在每次编译转换单元时都实际地读取并解析每个包含的头文件(包括递归包含的头文件)。

**bits/stdc++的优点**

- 在比赛中,使用这个文件是一个好主意,当你想减少时间浪费在做选择的时候;特别是当你的排名对时间很敏感的时候。
- 这还减少了编写所有必要头文件的所有杂务。
- 你不必为使用的每个函数都记住GNU c++的所有STL。