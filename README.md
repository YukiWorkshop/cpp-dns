# cpp-dns
[![pipeline status](https://gitlab.com/ReimuNotMoe/cpp-dns/badges/master/pipeline.svg)](https://gitlab.com/ReimuNotMoe/cpp-dns/commits/master)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/5ce2d6fb2bce40ea81a96e2554dbb5c8)](https://www.codacy.com/gh/YukiWorkshop/cpp-dns)
[![coverage report](https://gitlab.com/ReimuNotMoe/cpp-dns/badges/master/coverage.svg)](https://gitlab.com/ReimuNotMoe/cpp-dns/commits/master)

C++ async DNS resolver using UDNS and Boost.

## Requirements
Reasonably new versions of:
-  C++17 compatible compiler
-  CMake
-  Boost
-  [UDNS](https://www.corpit.ru/mjt/udns.html)

Quick command for Ubuntu users:
```shell script
apt install build-essential cmake libboost-all-dev libudns-dev
``` 

## Install
Use of Git submodule and CMake subdirectory is recommended.

```shell script
mkdir cpp_modules && cd cpp_modules
git submodule add https://github.com/YukiWorkshop/cpp-dns
```

```cmake
add_subdirectory(cpp_modules/cpp-dns)
include_directories(cpp_modules/cpp-dns)
target_link_libraries(your_project cpp-dns)
```

## Usage
```cpp
#include <cpp-dns.hpp>

using namespace YukiWorkshop;
```

Create a new resolver instance with default nameservers.

```cpp
DNSResolver d(io_svc);
```

Or with custom nameservers.

```cpp
DNSResolver d(io_svc, {"1.1.1.1", "8.8.8.8", "8.8.4.4"});
```

Get A records of `google.com`.

```cpp
d.resolve_a4("google.com", [](int err, auto& addrs, auto& qname, auto& cname, uint ttl){
    if (!err) {
        std::cout << "Query: " << qname << "\n";
        std::cout << "CNAME: " << cname << "\n";
        std::cout << "TTL: " << ttl << "\n";

        for (auto &it : addrs) {
            std::cout << "A Record: " << it.to_string() << "\n";
        }
    }
});
```

Get `matrix` SRV records of `riot.im`.

```cpp
d.resolve_srv("riot.im", "matrix", "tcp", [&](int err, auto& hosts, auto& qname, auto& cname, uint ttl){
    if (!err) {
        std::cout << "Query: " << qname << "\n";
        std::cout << "CNAME: " << cname << "\n";
        std::cout << "TTL: " << ttl << "\n";

        for (auto &it : hosts) {
            std::cout << "SRV Record: " << it.priority << " " << it.weight << " " << it.port << " "  << it.name << "\n";
        }
    }
});
```

See what's going wrong.

```cpp
d.resolve_a4("kefpdngjvoeqjgp3rnskldv", [&](int err, auto& addrs, auto& qname, auto& cname, uint ttl){
    assert(err);
    std::cout << "Oops. " << DNSResolver::error_string(err) << "\n";
});
```

See [Test.cpp](https://github.com/YukiWorkshop/cpp-dns/blob/master/Test.cpp) for more examples.

Callback definitions are in [DNSResolver.hpp](https://github.com/YukiWorkshop/cpp-dns/blob/master/DNSResolver.hpp).

Important notice: You need to prevent `io_service` from stopping. Otherwise you'll need to recreate `DNSResolver` since the UDP socket will get removed from `io_service` when it stops.

## License
LGPL
