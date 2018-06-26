from tkinter import * 
from collections import deque
import random
import time

class Window(Frame):

	def __init__(self, master=None,rows=4,cols=4,mine_num=2):
		# parameters that you want to send through the Frame class. 
		Frame.__init__(self, master)   
		self.pack()

		# Keep track of how many cells that are revealed 
		self.num_revealed_cells = 0

		self.rows = rows
		self.cols = cols
		self.mine_num = mine_num  

		### The total number of cells 
		self.total_cells_num = self.cols * self.rows 

		#reference to the master widget, which is the tk window
		self.master = master

		# Used in create_board: contains a list of each cell index Range(0,(rows*cols-1))
		# Can be accessed like: self.frame_list[0] ... self.frame_list[self.total_cells_num - 1]
		self.frame_list = []

		# 0 - uncliked 旗なし
		# 1 - clicked　旗あり
		# 2 - unclickable すでにマスが開けられている
		self.check_clicked = []

		# 旗の配置場所を格納するリスト
		# Containes the cell number that is flagged 
		self.flagged_cells = []

		# 地雷の配置場所を格納するリスト
		# List of each cell numbers that contains the bomb
		self.bomb_list = []

		# To check how many bombs arounded to the cell
		self.bomb_count = 0
		# To check how many bombs arounded to the cell
		self.around_list = []

		###
		#self.create_window()

		self.init_frame()
		 

	def init_frame(self):
		###Creating the main frame for the game
		#OUTER FRAME
		self.root_frame = Frame(self, relief = 'groove', borderwidth = 5, bg = 'LightGray')

		#Upper Status Frame
		self.status_frame = Frame(self.root_frame,height = 50, relief = 'sunken', borderwidth = 3, bg = 'LightGray')

		#Actual Game Frame
		self.game_frame = Frame(self.root_frame, relief = 'sunken', borderwidth = 3, bg = 'LightGray')
		
		# Creates the actual board
		self.create_board()

		# Having some paddings to space each other
		self.root_frame.pack()
		self.status_frame.pack(pady = 5, padx = 5, fill=X)
		self.game_frame.pack(pady = 5, padx = 5)

	# Creating cells
	def create_board(self):
		#各マス目に番号を振っておくといろいろ便利なのでそれ用の変数iを定義する
		i = 0
		#繰り返し作成したフレーム格納用リスト
		#self.frame_list = []
		for x in range(self.rows):
			for y in range(self.cols):
				#タテヨコ30pxの小さいフレームを量産。reliefをraisedにして出っ張り感を再現する
				self.frame=Frame(self.game_frame, width = 25, height=25, bd = 2, relief = 'raised', bg = 'LightGray')
				
				# Binded to left click
				#bindメソッドを使うと、そのオブジェクトにイベントを定義できる。
				#第一引数に<1>を指定すると左クリックした際のイベントとなる
        		#第二引数には呼び出される関数（4行目から定義しているleft_click関数）を記述する
				self.frame.bind("<Button-1>", self.left_click)

				#Binded to right click
				self.frame.bind("<Button-2>", self.right_click)

				# Cell numbering starts from 0 to (row * cols - 1)
				#frameにnumアトリビュートを定義する
				self.frame.num = i
				#作成したフレームをフレームのリストに格納する。これでインデックス番号でアクセスすることで
        		#各フレームを操作できる
				self.frame_list.append(self.frame)
				self.check_clicked.append(0)
				#gridを使ってフレームを配置する。packと違いgridを使うと、タテヨコ均等に9列x9列に配置できる
        		#rowでヨコ、columnでタテを指定している
				self.frame.grid(row=x,column=y)
				i+= 1
		#print(self.frame_list)
		#print(len(self.check_clicked))
		

	def left_click(self,event):
		#print(event.widget.num)
		### 旗がついているところをクリックしても何もできないようにする
		if self.check_clicked[event.widget.num] == 1:
			pass

		else:
			#event.#widgetで該当のオブジェクト（ウィジェット=部品）を取得できる
	    	#クリックした感を出すので、relefをridgeに変更する
			event.widget.configure(relief="ridge",bd='1')
			#またそのフレームのアトリビュートnumを表示する
			#event.widget.num contains index of the cell
			#print(event.widget.num)
			self.except_num = event.widget.num

			#地雷を配置
			#最初にクリックしたところは絶対地雷ではない
			#それ以外の場所からランダムに決める
			#地雷が配置されていない最初のクリックの時に呼ばれる
			if len(self.bomb_list) == 0:
				
				#地雷の個数が self.mine_num コになるまで繰り返し呼ばれる
				while len(self.bomb_list) != self.mine_num:
					bomb_num = random.randint(0, (self.cols * self.rows -1))
					#地雷がクリックした場所以外、かつ数字に重複が無い場合に新しく追加される
					#条件（bomb_num != except_num）で、生成したランダムな値がクリックしたマスとは違う判定
					#条件（(bomb_num in bomb_list) == False）で、地雷がすでにappendされてbomb_list
					###に格納されていないかの判定
					if bomb_num != self.except_num and (bomb_num in self.bomb_list) == False:
						self.bomb_list.append(bomb_num)

			#print("bomb places are:", self.bomb_list)

				# for i in self.bomb_list:
				# 	self.frame_list[i].config(bg="red")

			# bomb_count has the number of bombs that are surrounded to the clicked cell
			self.bomb_count = self.search_bomb(self.bomb_list, event.widget.num)

			# 爆弾以外に全部クリックし終えたら

			### Update the number of the cells that are opened
			self.num_revealed_cells+= 1

			# クリックしたマスが爆弾だったら
			if self.bomb_count == 9:
				
				#print("BOMB!")
				for i in self.bomb_list:
					# 全部の地雷の部分を赤色にするコード
					self.frame_list[i].configure(bg='red')
				
				for i in self.frame_list:
					#　ゲーム終了なので他のマスも左クリックできないようにする
					i.bind("<Button-1>", self.stop)

					# ゲーム終了なので他のマスも右クリックできないようにする
					i.bind("<Button-2>", self.stop)

				# Display an fram inside of status_frame 
				# ゲームオーバー

				# check if the flagged cell truly has bomb 
				# If the user flagged an empty space, change it to blue 
				# print(self.flagged_cells)
				# print(self.bomb_list)
				for i in self.flagged_cells:
					if i not in self.bomb_list:
						self.frame_list[i].configure(bg="blue")
				self.lose()

			else:
				# 地雷が０以外なら　マスに　周囲の地雷数を表示
				if self.bomb_count != 0:
					self.check_clicked[event.widget.num] = 2
					# # Update the number of the cells that are opened
					# self.num_revealed_cells+= 1
					# 周囲の地雷の数を表示するためのラベル
					self.bomb_count_label = Label(event.widget, text = self.bomb_count, bg = 'LightGray') 
					# そのラベルをplaceメソッドで表示
					self.bomb_count_label.place(width = 21, height = 21)
					# 1度クリックしたマス目はクリックできないようにする
					event.widget.bind("<Button-1>", self.stop)

					event.widget.bind("<Button-2>", self.stop)

				# 地雷が０
				else:
				# Recursively find surrounding cells that contain 0 bomb_count 
					self.check_clicked[event.widget.num] = 2
					# self.num_revealed_cells+= 1
					self.revealing_cells(event.widget.num)

		if self.num_revealed_cells == (self.total_cells_num - self.mine_num):
			
				# Display all the bombs
			for i in self.bomb_list:
				# 全部の地雷の部分を赤色にするコード
				self.frame_list[i].configure(bg='red')
			self.win()
				######################
		# print("Number of revealed cell:" ,self.num_revealed_cells)
		# print("Total number of cells on the board:",self.total_cells_num)
		# print("Bomber number: ",self.mine_num)
		# print("Actional bombs:", len(self.bomb_list))
		# print("Locations of bombs are:", self.bomb_list)


					
	def check_tile(self, num, queue):
		# If the cell number is valid
		if num >=0 and num < self.total_cells_num:
			# Find the bomb number of that cell
			bomb_n = self.search_bomb(self.bomb_list, num)

			# If it hasn't been clicked and no bomb nearby.
			if self.check_clicked[num] == 0 and bomb_n != 9:
				
				self.check_clicked[num] = 2
				self.num_revealed_cells+= 1

				if bomb_n == 0:
					self.frame_list[num].configure(relief="ridge",bd='1')
					queue.append(num)
				# If there are nearby mines
				else:
					self.frame_list[num].configure(relief="ridge",bd='1')
					self.bomb_count_label = Label(self.frame_list[num], text = bomb_n, bg = 'LightGray') 
					# そのラベルをplaceメソッドで表示
					self.bomb_count_label.place(width = 21, height = 21)
					
				# 1度クリックしたマス目はクリックできないようにする
				self.frame_list[num].bind("<Button-1>", self.stop)
				self.frame_list[num].bind("<Button-2>", self.stop)

######Step to open adjacent empty cells #######################################################
# Initialize a queue
# If current square is non-mine uncover it and add to queue, otherwise gameover
# Remove a square from queue
# Count mines adjacent to it
# If adjacent mine count is zero, add any adjacent covered squares to queue and uncover them
# Go to step 3 if queue is not empty, otherwise finish
###############################################################################################
	def revealing_cells(self, num):
		queue = deque([num])
		while len(queue) !=0:
			key = queue.popleft()
			if key % self.cols == 0:
				self.check_tile(key-self.cols, queue)
				self.check_tile(key-(self.cols-1), queue)				
				self.check_tile(key+1, queue)
				self.check_tile(key+self.cols, queue)
				self.check_tile(key+(self.cols+1), queue)

			elif key % self.cols == (self.cols-1): 
				self.check_tile(key-self.cols, queue)
				self.check_tile(key-(self.cols-1), queue)
				self.check_tile(key-1, queue)
				self.check_tile(key+(self.cols-1), queue)
				self.check_tile(key+self.cols, queue)
			
			elif key < self.cols:
				self.check_tile(key-1, queue)
				self.check_tile(key+1, queue)
				self.check_tile(key+(self.cols-1), queue)
				self.check_tile(key+self.cols, queue)
				self.check_tile(key+(self.cols+1), queue)
				
			elif key > (self.rows * self.cols - self.cols):
				self.check_tile(key-(self.cols+1), queue) 
				self.check_tile(key-self.cols, queue)
				self.check_tile(key-(self.cols-1), queue)
				self.check_tile(key-1, queue)
				self.check_tile(key+1, queue)
			else:
				self.check_tile(key-(self.cols+1), queue) 
				self.check_tile(key-self.cols, queue)
				self.check_tile(key-(self.cols-1), queue)
				self.check_tile(key-1, queue)
				self.check_tile(key+1, queue)
				self.check_tile(key+(self.cols-1), queue)
				self.check_tile(key+self.cols, queue)
				self.check_tile(key+(self.cols+1), queue)
			
	def right_click(self, event):
	# まずそのマスがすでに flag ではないと確認
	# 0 = unclicked, 1 = clicked
	# まだマスは　”ridge” 凹んでいない場合
		if self.check_clicked[event.widget.num] == 0:
			# 凹ませる
			event.widget.configure(relief="ridge",bd='1')

			# 旗の配置場所をリストに格納
			self.flagged_cells.append(event.widget.num)

			# 旗のついた印として黄色にマスを変更させる
			self.frame_list[event.widget.num].configure(bg="yellow")

			#　旗を配置したという情報
			self.check_clicked[event.widget.num] = 1

		# 凹んでいた場合
		elif self.check_clicked[event.widget.num]==1:
			#凸にする
			event.widget.configure(relief="raised", bd='1')

			if event.widget.num in self.flagged_cells:
				self.flagged_cells.remove(event.widget.num)

			# マスの色を元に戻す
			self.frame_list[event.widget.num].configure(bg ="LightGray")

			self.check_clicked[event.widget.num] = 0

		# すでにマスが開けられている状態
		else:
			pass # Do nothing



	### list = bomb_list which contains the index of bombs placed
	### num = index of what user just clicked 
	# 地雷の個数を判定する関数
	def search_bomb(self, list, num):
		self.bomb_count = 0
		self.around_list = []
		#クリックしたマス目の番号がリストの中に存在するパターン
		### If the one that clicked is the bomb:
		if num in list:
			return 9
		#左端の列の判定
		if num % self.cols == 0:
			self.around_list.append(num - self.cols)
			self.around_list.append(num - (self.cols - 1))
			self.around_list.append(num + 1)
			self.around_list.append(num + self.cols)
			self.around_list.append(num + (self.cols + 1))
		#右端
		elif num % self.cols == (self.cols-1):
			self.around_list.append(num - (self.cols + 1))
			self.around_list.append(num - self.cols)
			self.around_list.append(num - 1)
			self.around_list.append(num + (self.cols - 1))    
			self.around_list.append(num + self.cols)   
		#上端 
		elif num < self.cols:
			self.around_list.append(num - 1)
			self.around_list.append(num + 1)
			self.around_list.append(num + (self.cols - 1))
			self.around_list.append(num + self.cols)
			self.around_list.append(num + (self.cols + 1))
		#下端
		elif num > (self.rows * self.cols - self.cols):
			self.around_list.append(num - (self.cols + 1))
			self.around_list.append(num - self.cols)
			self.around_list.append(num - (self.cols - 1))
			self.around_list.append(num - 1)
			self.around_list.append(num + 1)
		#それ以外の端ではないマス目
		else:
			self.around_list.append(num - (self.cols + 1))
			self.around_list.append(num - self.cols)
			self.around_list.append(num - (self.cols - 1))
			self.around_list.append(num - 1)
			self.around_list.append(num + 1)
			self.around_list.append(num + (self.cols - 1))
			self.around_list.append(num + self.cols)
			self.around_list.append(num + (self.cols + 1))
		#周囲のマス目の番号をfor-in文で1つずつ取得し、その値が地雷番号のリストにあるかどうかを判定
		for i in self.around_list:
			if i in list:
				#ある場合は地雷の数を表すbomb_countを1ずつ増やす
				self.bomb_count += 1
		#その地雷の数を返す
		return self.bomb_count 

	#関数定義のブロックには、何も処理を行わないことを表すpassを記述
	def stop(self,event):
		pass

	def reset(self):
		self.destroy()

		
	def reset_button(self):
		Button(self.status_frame, text="reset", command=self.reset).pack()

	def lose(self):
		#print(self.num_revealed_cells)
		#print(self.flagged_cells)
		Label(self.status_frame, fg="black", bg="LightGray", font = "Helvetica 20 bold", text="YOU LOST!!").pack()
		self.reset_button()

	### もし合計マス-(爆弾の数+旗の数)＝開けたマスの数
	### 同時にまず　もし旗のついているマスが爆弾かどうかを確認
	def win(self):
		Label(self.status_frame, fg="black", bg="LightGray", font = "Helvetica 20 bold", text="YOU WIN!!").pack()
		self.reset_button()

##################################################################
##################################################################
def quit():
    root.destroy()

############################################################
def draw():
	global root
	root = Tk()
	root.title("MINESWEEPER GAME")
	# root.geometry("500x600")
	# root.resizable(0,0)

	### create a toplevel menu

	menubar = Menu(root)

	### Pulldown menues
	subMenu = Menu(menubar, tearoff=0)
	
	menubar.add_cascade(label='New Game', menu=subMenu)
	subMenu.add_command(label="9*9 with 10 mines", command=lambda: Window(root, 9, 9, 10))
	subMenu.add_command(label="16*16 with 40 mines", command=lambda: Window(root, 16, 16, 40))
	subMenu.add_command(label="16*30 with 99 mines", command=lambda: Window(root, 16, 30, 99))
	subMenu.add_separator()
	#subMenu.add_command(label="Exit", command=lambda:sys.exit(1))
	subMenu.add_command(label="Exit", command=quit)
	
	### display the menu
	root.config(menu=menubar)
###############################################################################
	text = Label(root,  text="Minesweeper", font = "Helvetica 35 bold",fg = "light green")
	description = Label(root, fg = "light blue", font = "Helvetica 16 bold", text="Press Left-Click to Reveal Square \n Press Right-Click to Flag Mines \n Press Right-Click again to Unflag Mines \n Start New Game at the top")
	
	easy = Button(root, fg = "deep sky blue" ,text="9x9 map with 10 bombs", font = "Helvetica 20 bold ",command=lambda: Window(root, 9, 9, 10))

	medium = Button(root, fg = "turquoise", text="16x16 map with 40 bombs", font = "Helvetica 20 bold ",
	             command=lambda: Window(root, 16, 16, 40))

	hard = Button(root, fg = "light sea green", text="16x30 map with 99 bombs", font = "Helvetica 20 bold",
	             command=lambda: Window(root, 16, 30, 99))

	text.pack(fill=X,padx=10,pady=10)
	description.pack(fill=X,padx=10)
	easy.pack(padx=5, pady=3, side=TOP,  fill=BOTH, expand=True)
	medium.pack(padx=5, pady=3, side=TOP, fill=BOTH,expand=True)
	hard.pack(padx=5, pady=3,side=TOP, fill=BOTH,expand=True)
###################################################################
	root.protocol("WM_DELETE_WINDOW", quit)
	root.mainloop()

if __name__ == "__main__":
	draw()
