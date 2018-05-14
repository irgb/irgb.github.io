---
title: Hash
date: 2018-05-14
categories: algorithm
tags: [algorithm hash]

---


### hash 函数
比较常用的是 [murmurhash3](https://github.com/aappleby/smhasher/wiki/MurmurHash3) :
**murmurhash3.h**
```cpp
#ifndef MURMURHASH3_H_
#define MURMURHASH3_H_

// Referenec: https://github.com/goossaert/hashmap
#include <stdint.h>

void murmurhash3_x64_128 (const void* key, int len, uint32_t seed, void * out);

#endif // MURMURHASH3_H_
```
**murmurhash3.cpp**
```cpp
#include <stdlib.h>

#include "murmurhash3.hpp"

#define FORCE_INLINE inline __attribute__((always_inline))

inline uint64_t rotl64 (uint64_t x, int8_t r) {
    return (x << r) | (x >> (64 - r));
}

#define ROTL64(x, y) rotl64(x, y)

#define BIG_CONSTANT(x) (x##LLU)

FORCE_INLINE uint64_t getblock64 (const uint64_t * p, int i) {
    return p[i];
}

FORCE_INLINE uint64_t fmix64 (uint64_t k) {
    k ^= k >> 33;
    k *= BIG_CONSTANT(0xff51afd7ed558ccd);
    k ^= k >> 33;
    k *= BIG_CONSTANT(0xc4ceb9fe1a85ec53);
    k ^= k >> 33;

    return k;
}

void murmurhash3_x64_128 (const void * key, const int len,
		const uint32_t seed, void * out) {
    const uint8_t * data = (const uint8_t*)key;
    const int nblocks = len / 16;

    uint64_t h1 = seed;
    uint64_t h2 = seed;

    const uint64_t c1 = BIG_CONSTANT(0x87c37b91114253d5);
    const uint64_t c2 = BIG_CONSTANT(0x4cf5ad432745937f);

    // body
    const uint64_t * blocks = (const uint64_t *)(data);

    int i = 0;
    for (; i < nblocks; i++) {
        uint64_t k1 = getblock64(blocks,i*2+0);
        uint64_t k2 = getblock64(blocks,i*2+1);
        k1 *= c1; k1    = ROTL64(k1,31); k1 *= c2; h1 ^= k1;
        h1 = ROTL64(h1,27); h1 += h2; h1 = h1*5+0x52dce729;
        k2 *= c2; k2    = ROTL64(k2,33); k2 *= c1; h2 ^= k2;
        h2 = ROTL64(h2,31); h2 += h1; h2 = h2*5+0x38495ab5;
    }

    // tail
    const uint8_t * tail = (const uint8_t*)(data + nblocks*16);

    uint64_t k1 = 0;
    uint64_t k2 = 0;

    switch (len & 15) {
		case 15: k2 ^= ((uint64_t)tail[14]) << 48;
		case 14: k2 ^= ((uint64_t)tail[13]) << 40;
		case 13: k2 ^= ((uint64_t)tail[12]) << 32;
		case 12: k2 ^= ((uint64_t)tail[11]) << 24;
		case 11: k2 ^= ((uint64_t)tail[10]) << 16;
		case 10: k2 ^= ((uint64_t)tail[ 9]) << 8;
		case 9 : k2 ^= ((uint64_t)tail[ 8]) << 0;
				 k2 *= c2; k2 = ROTL64(k2,33); k2 *= c1; h2 ^= k2;

		case 8: k1 ^= ((uint64_t)tail[ 7]) << 56;
		case 7: k1 ^= ((uint64_t)tail[ 6]) << 48;
		case 6: k1 ^= ((uint64_t)tail[ 5]) << 40;
		case 5: k1 ^= ((uint64_t)tail[ 4]) << 32;
		case 4: k1 ^= ((uint64_t)tail[ 3]) << 24;
		case 3: k1 ^= ((uint64_t)tail[ 2]) << 16;
		case 2: k1 ^= ((uint64_t)tail[ 1]) << 8;
		case 1: k1 ^= ((uint64_t)tail[ 0]) << 0;
				 k1 *= c1; k1 = ROTL64(k1,31); k1 *= c2; h1 ^= k1;
    };

    //----------
    // finalization

    h1 ^= len; h2 ^= len;

    h1 += h2;
    h2 += h1;

    h1 = fmix64(h1);
    h2 = fmix64(h2);

    h1 += h2;
    h2 += h1;

    ((uint64_t*)out)[0] = h1;
    ((uint64_t*)out)[1] = h2;
}
```
**调用方法**:
```cpp
char out[16];
uint64_t key = 98723849823;
murmurhash3_x64_128((void*)(&key), sizeof(key), 0, out);
uint64_t hashcode = *((uint64_t*) out) ^ *((uint64_t*) (out + 8));// xor 不改变随机数分布
```

### hash 表冲突解决策略:
比较常用的是 robin hood hashing:
**robin_hood_hash.hpp**
```cpp
#ifndef ROBIN_HOOD_HASHTABLE_H_
#define ROBIN_HOOD_HASHTABLE_H_
#include <utility>
#include <algorithm>
#define likely(x) __builtin_expect(!!(x), 1)
#define unlikely(x) __builtin_expect(!!(x), 0)

// 根据具体场景优化定制的的 robin hood hashtable, 通用型不强.
// key 的类型为 uint64_t, 即 winfoid 的类型, value 的类型为 uint32_t.
// 只支持插入和查找, 不支持删除, 不支持自动扩容.
// 使用时需要用户自己指定 capacity, 这个 capacity 决定了最终的 load factor

typedef uint64_t K;
typedef uint32_t V;

class RobinHoodHashtable {
public:
    struct Item {
        K key;
        V value;
        Item() : key(0), value(0) {}
        Item(K k, V v) : key(k), value(v) {}
    };

    RobinHoodHashtable(): _capacity(0), _item_num(0), _buffer(nullptr), _probing_num(nullptr) {}

    ~RobinHoodHashtable() {
        if (_buffer != nullptr) {
            delete [] _buffer;
        }
        if (_probing_num != nullptr) {
            delete [] _probing_num;
        }
    }

    // 初始化 hashtable, 设置 capacity
    bool init(uint32_t capacity) {
        _capacity = capacity;
        _buffer = new (std::nothrow) Item[_capacity];
        if (_buffer == nullptr) {
            std::cout << "new buffer array failed." << std::endl;
            return false;
        }
        _probing_num = new (std::nothrow) int32_t[_capacity];
        if (_probing_num == nullptr) {
            std::cout << "new probing_num array failed." << std::endl;
            return false;
        }
        std::fill_n(_probing_num, _capacity, -1);
        return true;
    }

    // 插入
    bool insert(K key, V value) {
        if (_item_num >= _capacity) {
            std::cout << "you need to increase capacity" << std::endl;
            return false;
        }

        uint32_t pos = _key_to_pos(key);
        uint32_t probing_count = 0;
        while (true) {
            // 如果 pos 未被占用就放在这个位置
            if (_probing_num[pos] == -1) {
                new (_buffer + pos) Item(key, value);
                _probing_num[pos] = probing_count;
                break;
            }
            // 相同的 key 就覆盖 value
            if (key == _buffer[pos].key) {
                return false;
            }
            // 否则继续探测下一个位置
            uint32_t cur_probing_count = _probing_num[pos];
            if (probing_count > cur_probing_count) {
                std::swap(key, _buffer[pos].key);
                std::swap(value, _buffer[pos].value);
                _probing_num[pos] = probing_count;
                probing_count = cur_probing_count;
            }
            pos = ++pos >= _capacity ? pos % _capacity : pos;
            ++probing_count;
        }
        ++_item_num;
        return true;
    }

    // 根据 key 查找, 如果找到返回 <true, value>, 没有找到返回<false, 0>
    __attribute__((always_inline)) std::pair<bool, V> find(const K& key) {
        uint32_t pos = _key_to_pos(key);
        uint32_t probing_count = 0;
        while (true) {
            if (_probing_num[pos] == -1 || probing_count > _probing_num[pos]) {
                return std::make_pair(false, 0);
            } else if (_buffer[pos].key == key) {
                return std::make_pair(true, _buffer[pos].value);
            }
            pos = ++pos >= _capacity ? pos % _capacity : pos;
            ++probing_count;
        }
    }

private:
    uint32_t _item_num;
    uint32_t _capacity;
    // 存储 k-v 对的缓冲区
    Item * _buffer;
    // 记录一个元素的探测长度, -1 表示当前位置没有元素
    int32_t* _probing_num;
    // 将 key 转换为pos, 通常可以通过murmurhash3_x64_128(key) 计算 hashcode, 然后在计算 pos, 但实验发现 murmurhash3_x64_128 本身也比较耗时, 直接对 key 进行取余效率更高. 但这种情况只适合数据分布比较均匀的情况, 否则有可能出现insert 和 find 冲突大大增加的情况.
    __attribute__((always_inline)) uint32_t _key_to_pos(const K& key) {
        uint32_t* p = (uint32_t*) &key;
        uint32_t hashcode = *p ^ *(p+1);
        uint32_t pos = hashcode >= _capacity ? hashcode % _capacity : hashcode;
        return pos;
    }
};
#endif // ROBIN_HOOD_HASHTABLE_H_
```
### Reference
- [Robin Hood Hashing should be your default Hash Table implementation](https://www.sebastiansylvan.com/post/robin-hood-hashing-should-be-your-default-hash-table-implementation/)
- [Writing a Damn Fast Hash Table With Tiny Memory Footprints](http://www.idryman.org/blog/2017/05/03/writing-a-damn-fast-hash-table-with-tiny-memory-footprints/)
- [Robin Hood hashing](http://codecapsule.com/2013/11/11/robin-hood-hashing/)
- [Benchmark of major hash maps implementations](https://tessil.github.io/2016/08/29/benchmark-hopscotch-map.html)
- [I Wrote The Fastest Hashtable](https://probablydance.com/2017/02/26/i-wrote-the-fastest-hashtable/)
