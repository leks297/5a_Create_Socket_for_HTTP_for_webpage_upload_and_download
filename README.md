# 5a_Create_Socket_for_HTTP_for_webpage_upload_and_download
## AIM :
To write a PYTHON program for socket for HTTP for web page upload and download
## Algorithm

1.Start the program.
<BR>
2.Get the frame size from the user
<BR>
3.To create the frame based on the user request.
<BR>
4.To send frames to server from the client side.
<BR>
5.If your frames reach the server it will send ACK signal to client otherwise it will send NACK signal to client.
<BR>
6.Stop the program
<BR>
## Program 
## file_transfer.py
```
import socket
import threading
import time

HOST = 'localhost'
PORT = 5050

def server():
    s = socket.socket()
    s.bind((HOST, PORT))
    s.listen(5)

    while True:
        conn, addr = s.accept()
        request = conn.recv(4096).decode(errors='ignore')

        if request.startswith("POST"):
            body = request.split("\r\n\r\n", 1)[1]
            with open("uploaded.txt", "w") as f:
                f.write(body)
            response = "HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n<html><body><h2>File Uploaded Successfully</h2></body></html>"

        elif request.startswith("GET"):
            try:
                with open("uploaded.txt", "r") as f:
                    data = f.read()
                response = f"HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n<html><body><pre>{data}</pre></body></html>"
            except:
                response = "HTTP/1.1 404 Not Found\r\nContent-Type: text/html\r\n\r\n<html><body><h2>File Not Found</h2></body></html>"

        else:
            response = "HTTP/1.1 400 Bad Request\r\n\r\n"

        conn.send(response.encode())
        conn.close()

def client():
    s = socket.socket()
    s.connect((HOST, PORT))

    with open("example.txt", "r") as f:
        data = f.read()

    request = f"POST /upload HTTP/1.1\r\nHost: {HOST}\r\nContent-Length: {len(data)}\r\n\r\n{data}"
    s.send(request.encode())

    print(s.recv(4096).decode())
    s.close()

    s = socket.socket()
    s.connect((HOST, PORT))

    request = f"GET /file HTTP/1.1\r\nHost: {HOST}\r\n\r\n"
    s.send(request.encode())

    response = s.recv(4096).decode()
    print(response)

    content = response.split("\r\n\r\n", 1)[1]

    with open("downloaded.txt", "w") as f:
        f.write(content)

    print("File downloaded successfully")
    s.close()

if __name__ == "__main__":
    threading.Thread(target=server, daemon=True).start()
    time.sleep(1)
    client()
```
## OUTPUT
<img width="975" height="408" alt="Screenshot 2026-03-25 143523" src="https://github.com/user-attachments/assets/136d8b8c-bc7d-44e3-8574-025509d2a9e9" />

## Result
Thus the socket for HTTP for web page upload and download created and Executed
