# -*- coding: utf-8 -*-
"""
Created on Sat Jun 29 03:15:48 2019

@author: Amogh Banerjee
"""

import cv2
import numpy as np
import random

class QuickUnionUF:

    def __init__(self, N):
        self.id = list(range(N))
        self.sz = [0] * N

    @classmethod
    def fromimage(self, im):
        self.id = im
        self.sz = [0] * len(im)

    def root(self, i):
        while (i != self.id[i]):
            i = self.id[i]
        return i

    def getresult(self):
        result = [self.root(i) for i in self.id]
        return result

    def connected(self, p, q):
        return self.root(p) == self.root(q)

    def union(self, p, q):
        i = self.root(p)
        j = self.root(q)

        if (i == j):
            return
        if (self.sz[i] < self.sz[j]):
            self.id[i] = j
            self.sz[j] += self.sz[i]
        else:
            self.id[j] = i
            self.sz[j] += self.sz[i]        

def bwlabel(im):

    M, N = im.shape[:2]
    qf = QuickUnionUF(M * N)
    for i in range(M - 1):
        for j in range(N - 1):
            if (im[i][j] == im[i][j+1]):
                qf.union(i * N + j, i * N + j + 1)
            if (im[i + 1][j] == im[i][j]):
                qf.union(i * N + j, (i + 1) * N + j)

    mask = np.reshape(np.array(qf.getresult()), (M, N))
    values = np.unique(mask).tolist()

    random.seed()
    colors = [(random.randint(0,255), random.randint(0,255), random.randint(0,255)) for k in range(len(values))]

    out = np.zeros((M, N, 3))
    for i in range(M):
        for j in range(N):
            label = values.index(mask[i][j])
            out[i,j] = colors[label]

    return out

im = cv2.imread("bw.jpg",cv2.IMREAD_GRAYSCALE)
out = bwlabel(im > 100)
cv2.imwrite("result.png", out)