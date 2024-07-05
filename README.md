# Midori
Midori is one of Lightweight Cryptographies.


#sb0:Midori64, sb1:Midori128
sb0_hex = [0xc, 0xa, 0xd, 0x3, 0xe, 0xb, 0xf, 0x7, 0x8, 0x9, 0x1, 0x5, 0x0, 0x2, 0x4, 0x6]
sb0_decimal = [12, 10, 13, 3, 14, 11, 15, 7, 8, 9, 1, 5, 0, 2, 4, 6]
sb1_hex = [0x1, 0x0, 0x5, 0x3, 0xe, 0x2, 0xf, 0x7, 0xd, 0xa, 0x9, 0xb, 0xc, 0x8, 0x4, 0x6]
sb1_decimal = [1, 0, 5, 3, 14, 2, 15, 7, 13, 10, 9, 11, 12, 8, 4, 6]
alpha = [
        [0, 0, 0, 1, 0, 1, 0, 1, 1, 0, 1, 1, 0, 0, 1, 1],
        [0, 1, 1, 1, 1, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 0],
        [1, 0, 1, 0, 0, 1, 0, 0, 0, 0, 1, 1, 0, 1, 0, 1],
        [0, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 1, 1],
        [0, 0, 0, 1, 0, 0, 0, 0, 0, 1, 0, 0, 1, 1, 1, 1],
        [1, 1, 0, 1, 0, 0, 0, 1, 0, 1, 1, 1, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 1, 0, 0, 1, 1, 0, 0, 1, 1, 0],
        [0, 0, 0, 0, 1, 0, 1, 1, 1, 1, 0, 0, 1, 1, 0, 0],
        [1, 0, 0, 1, 0, 1, 0, 0, 1, 0, 0, 0, 0, 0, 0, 1],
        [0, 1, 0, 0, 0, 0, 0, 0, 1, 0, 1, 1, 1, 0, 0, 0],
        [0, 1, 1, 1, 0, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 1],
        [0, 0, 1, 0, 0, 0, 1, 0, 1, 0, 0, 0, 1, 1, 1, 0],
        [0, 1, 0, 1, 0, 0, 0, 1, 0, 0, 1, 1, 0, 0, 0, 0],
        [1, 1, 1, 1, 1, 0, 0, 0, 1, 1, 0, 0, 1, 0, 1, 0],
        [1, 1, 0, 1, 1, 1, 1, 1, 1, 0, 0, 1, 0, 0, 0, 0]
]

def main():
    key = input("鍵を入力")
    m = input("平文を入力")
    WK, k0, k1 = key_generation(key)
    round = 1
    S = KeyAdd(m, round, WK)
    round += 1
    for i in range(15):
        S = SubCell(S)
        S = ShuffleCell(S)
        S = MixColumn(S)
        if round % 2 == 0:
            S = KeyAdd(S, round, k0)
        else:
            S = KeyAdd(S, round, k1)
        round += 1
    S = SubCell(S)
    Y = KeyAdd(S, round, WK)
    result = []
    for i in Y:
        result.append(i[-1])
    print("".join(result))




def key_generation(key):
    k0 = key[:16]
    k1 = key[16:32]
    WK = []
    for i in range(16):
        WK.append(hex((int(k0[i], 16) ^ int(k1[i], 16))))
    return WK, k0, k1

def KeyAdd(S, round, k):
    global WK
    KeyAdd_tmp = []
    if (round == 1) or (round == 17):
        for i in range(16):
            KeyAdd_tmp.append(hex(int(S[i], 16) ^ int(k[i], 16)))
    else:
        RK = []
        for i in range(16):
            RK.append(int(k[i], 16) ^ alpha[round-2][i])
        for i in range(16):
            KeyAdd_tmp.append(hex(int(S[i], 16) ^ RK[i]))
    return KeyAdd_tmp

def SubCell(S):
    Subcell_tmp = []
    for i in range(16):
        Subcell_tmp.append(hex(sb0_decimal[int(S[i], 16)]))
    return Subcell_tmp
def ShuffleCell(S):
    shuffle_l = [0, 10, 5, 15, 14, 4, 11, 1, 9, 3, 12, 6, 7, 13, 2, 8]  
ShuffleCell_tmp = [0] * 16
for i, sl in enumerate(shuffle_l):
    ShuffleCell_tmp[i] = S[sl]
return ShuffleCell_tmp  


def MixColumn(S):
    S_int = []
    for i in range(16):
        S_int.append(int(S[i], 16))
    MixColumn_tmp = []
    for i in range(4):
        MixColumn_tmp.append(hex(S_int[4*i+1] ^ S_int[4*i+2] ^ S_int[4*i+3]))
        MixColumn_tmp.append(hex(S_int[4*i] ^ S_int[4*i+2] ^ S_int[4*i+3]))
        MixColumn_tmp.append(hex(S_int[4*i] ^ S_int[4*i+1] ^ S_int[4*i+3]))
        MixColumn_tmp.append(hex(S_int[4*i] ^ S_int[4*i+1] ^ S_int[4*i+2]))
    return MixColumn_tmp


if __name__ == "__main__":
    main()
