#  coding: utf-8 
import SocketServer

import os.path

# Copyright 2013 Abram Hindle, Eddie Antonio Santos
# 
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
# 
#     http://www.apache.org/licenses/LICENSE-2.0
# 
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
#
# Furthermore it is derived from the Python documentation examples thus
# some of the code is Copyright © 2001-2013 Python Software
# Foundation; All Rights Reserved
#
# http://docs.python.org/2/library/socketserver.html
#
# run: python freetests.py

# try: curl -v -X GET http://127.0.0.1:8080/

# referenced the following document on file parsing:
# http://www.dotnetperls.com/file-python
class MyWebServer(SocketServer.BaseRequestHandler):

    def parseRequest(self, request):
	request = request.split("\r\n")
	

	##Assumed Defaults
	verb = ""
	directory = "/"
	protocol = "HTTP/1.1"
	mimetype = ""
	outString = ""
	
	for i in range(0,len(request)):
		if (request[i][:4] == "GET "):
			attributes = request[i].split(" ")
			verb = attributes[0]
			directory = attributes[1]
			protocol = attributes[2]
		##elif (request[i][:12].lower() == "content-type"):
		##	attributes = request[i].replace(" ","")
		##	attributes = attributes.split(":")
		##	mimetype = attributes[1]
	
	if(directory[-1] == "/"):
		directory = directory + "index.html"
		mimetype = "text/html"
	elif(directory[-5:] == ".html"):
		mimetype = "text/html"
	elif(directory[-4:] == ".css"):
		mimetype = "text/css"
	else:
		directory = directory + "/"
	
	directory = "www" + directory 	
	directory = directory.split("/")
	fileExist = True
	finalDepth = 0
	path = ""
	for level in range(1,len(directory)):
		path = ""
		depth = 0;
		for deep in range(0,level):
			if(directory[deep] != ""):
				depth += 1
				if (directory[deep] == "."):
					directory[deep] = ""
					depth -= 1
				if (directory[deep] == ".."):
					directory[deep] = ""
					directory[deep - 1] = ""
					depth -= 2
				path = path + directory[deep]
				if (deep != len(directory) -1):
					path = path + "/"
			finalDepth = depth
		
		exists = os.path.exists(path);
		if (exists == False):
			fileExist = False
		if (depth <= 0):
			fileExist = False
		print(path, finalDepth, exists)
	
	if (fileExist == False):
		return False
	elif (fileExist == True):
		path = ""
		for deep in range(0,len(directory)):
			if(directory[deep] != ""):
				depth += 1
				if (directory[deep] == "."):
					directory[deep] = ""
					depth -= 1
				if (directory[deep] == ".."):
					directory[deep] = ""
					directory[deep - 1] = ""
					depth -= 2
				path = path + directory[deep]
				if (deep != len(directory) -1):
					path = path + "/"
			finalDepth = depth
		
		newfile = open(path, "r")
		outString = "Content-Type:" + mimetype + "\r\n\r\n"
		for line in newfile.readlines():
			outString = outString + line
		return outString


    def handle(self):
        self.data = self.request.recv(1024).strip()
        print ("Got a request of: %s\n" % self.data)
	output = self.parseRequest(self.data)
	print(output)
	if (output == False):
		print("404 Status Sent")
		self.request.sendall("HTTP/1.1 404 NOT FOUND\r\n")
		self.request.sendall("Status-Code:404\r\n")
		self.request.sendall("<h1>404: It appears the page you are looking for does not exist</h1>\r\n\r\n")
		
	else:
		
		self.request.sendall("HTTP/1.1 200 OK\r\n")
		self.request.sendall(output)
	
    

if __name__ == "__main__":
    HOST, PORT = "localhost", 8080

    SocketServer.TCPServer.allow_reuse_address = True
    # Create the server, binding to localhost on port 8080
    server = SocketServer.TCPServer((HOST, PORT), MyWebServer)

    # Activate the server; this will keep running until you
    # interrupt the program with Ctrl-C
    server.serve_forever()
