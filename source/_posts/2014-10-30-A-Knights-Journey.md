title: "骑士周游-马踏棋盘问题(A Knight's Journey)"
date: 2014-10-30 14:30:28
tags:
	- python
categories:
    - programming
    - python
---

看到 @大城小胖 做了个H5游戏：[马踏棋盘](http://t.cn/R7azxtN)，于是想起研究一下这个算法。

题目是这样的：国际象棋的棋盘为8*8的方格棋盘，将“马”放在任意指定的方格中，按照“马”走棋的规则将“马”进行移动。要求每个方格只能进入一次，最终使得“马”走遍棋盘64个方格。（N=8的情况）

我用python实现了这个算法，其中用到了*回溯法*，并用*贪心法*进行优化，以防递归深度太深而溢出。当找到一个解就停止递归。

不过这个解的最后一步与初始位置难以重合，而@大城小胖 的游戏中却要求马最后要回到初始位置，因此算法还要进化

<!-- more -->


``` python
N = 6
D = 8
offsetX = [-1,1,-1,1,-2,-2,2,2]
offsetY = [-2,-2,2,2,-1,1,-1,1]
chessBoard= [[0]*N for i in xrange(N)]


class ChessNode:
	def __init__(self, x, y):
		self.x, self.y = x, y
		self.wayout = 0

	def isCorrect(self, count=0):
		return self.x<N and self.x>=0 and self.y<N and self.y>=0 and (chessBoard[self.x][self.y]==0 or chessBoard[self.x][self.y]==1 and count==N*N+1)

	def setWayout(self, count=0):
		for direction in xrange(D):
			nx, ny = self.x + offsetX[direction], self.y + offsetY[direction]
			aNode = ChessNode(nx, ny)
			self.wayout += 1 if aNode.isCorrect(count=count) else 0
	def show(self):
		print '(%s,%s)=> %s wayouts' % (self.x, self.y, self.wayout)


class HorseJump:
	def success(self):
		for i in xrange(N):
			for j in xrange(N):
				print "%2d " % chessBoard[i][j],
			print
		print '====================='



	def greedyDFS(self, node, count):
		# node.show()
		# self.success()
		# print count 
		if self.found: return
		if count > N*N:
			# self.found = True
			self.success()
			return

		v = []	
		for direction in xrange(D):
			newNode = ChessNode(node.x+offsetX[direction], node.y+offsetY[direction])
			if newNode.isCorrect(count=count+1):
				newNode.setWayout(count=count+1)
				v.append(newNode)

		sv = sorted(v, lambda x,y: x.wayout < y.wayout)
		for it in sv:
			chessBoard[it.x][it.y] = count
			self.greedyDFS(it, count+1)
		chessBoard[node.x][node.y] = 0

	def greedyWalk(self, ox, oy):
		self.ox, self.oy = ox, oy
		node = ChessNode(ox, oy)
		if not node.isCorrect():
			print "illegal init x,y"
			return
		self.success()
		chessBoard[ox][oy]=1
		self.found = False
		self.greedyDFS(node, count=2)


h=HorseJump()
h.greedyWalk(4,3)


```

参考：
<http://blog.csdn.net/wangzhicheng1983/article/details/7871650>
<http://blog.csdn.net/cdu09/article/details/20400101>