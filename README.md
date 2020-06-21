# ProjetoIIIB
tcp E udp


Implementação dos Protocolos TCP E UDP na camada de transporte

Este relatório consiste em demostrar o desenvolvimento do uma aplicação utilizando os protocolos UDP e TCP.

A principal diferença que precisamos saber entre estes dois protocolos é a de que o protocolo TCP é orientado a conexão, isto quer dizer que como protocolo de transporte , é necessário primeiramente ficar estabelecida a conexão , tornando essa troca de informação mais segura, pois garante a entrega dos dados entre emissor e receptor.
Já o protocolo UDP não é orientado a conexão, é um protocolo um pouco mais simples e também menos seguro que o TCP, aonde não é fornecida garantia na entrega dos dados entre emissor e receptor.

Aplicação UDP
Para o funcionamento da Aplicação serão criadas duas classes, uma delas será o Servidor UDP que ficará Listening em uma determinada porta, e um Cliente para envio de mensagem

//CLASSE UDPServer

import java.io.*;
import java.net.*;
 
class UDPServer {
	public static void main(String args[]) throws Exception {
 
		int porta = 9876;
		int numConn = 1;
		
		DatagramSocket serverSocket = new DatagramSocket(porta);
 
		byte[] receiveData = new byte[1024];
		byte[] sendData = new byte[1024];
 
		while (true) {
 
			DatagramPacket receivePacket = new DatagramPacket(receiveData,
					receiveData.length);
			System.out.println("Esperando por datagrama UDP na porta " + porta);
			serverSocket.receive(receivePacket);
			System.out.print("Datagrama UDP [" + numConn + "] recebido...");
 
			String sentence = new String(receivePacket.getData());
			System.out.println(sentence);
			
			InetAddress IPAddress = receivePacket.getAddress();
 
			int port = receivePacket.getPort();
 
			String capitalizedSentence = sentence.toUpperCase();
 
			sendData = capitalizedSentence.getBytes();
 
			DatagramPacket sendPacket = new DatagramPacket(sendData,
					sendData.length, IPAddress, port);
			
			System.out.print("Enviando " + capitalizedSentence + "...");
 
			serverSocket.send(sendPacket);
			System.out.println("OK\n");
		}
	}
}

// Classe UDPCliente

import java.io.*;
import java.net.*;

class UDPClient {
	public static void main(String args[]) throws Exception {

		BufferedReader inFromUser = new BufferedReader(new InputStreamReader(
				System.in));

		DatagramSocket clientSocket = new DatagramSocket();

		String servidor = "localhost";
		int porta = 9876;

		InetAddress IPAddress = InetAddress.getByName(servidor);

		byte[] sendData = new byte[1024];
		byte[] receiveData = new byte[1024];

		System.out.println("Digite o texto a ser enviado ao servidor: ");
		String sentence = inFromUser.readLine();
		sendData = sentence.getBytes();
		DatagramPacket sendPacket = new DatagramPacket(sendData,
				sendData.length, IPAddress, porta);

		System.out
				.println("Enviando pacote UDP para " + servidor + ":" + porta);
		clientSocket.send(sendPacket);

		DatagramPacket receivePacket = new DatagramPacket(receiveData,
				receiveData.length);

		clientSocket.receive(receivePacket);
		System.out.println("Pacote UDP recebido...");

		String modifiedSentence = new String(receivePacket.getData());

		System.out.println("Texto recebido do servidor:" + modifiedSentence);
		clientSocket.close();
		System.out.println("Socket cliente fechado!");
	}
}

Aplicação TCP

Para a aplicação TCP serão utilizados os mesmos fundamentos da aplicação UDP, com a criação de um cliente e um servidor.

// Classe TCPServer

import java.io.*; 
import java.net.*; 
class TCPServer { 
	 public static void main( String argv[]) throws Exception 
	 { 
		 String clientSentence; 
		 String capitalizedSentence; 
		 ServerSocket welcomeSocket = new ServerSocket(6789); 
 
		 System.out.println("ponto1");			 		 
		 Socket connectionSocket = welcomeSocket.accept(); 			
					 
		 while (true) { 
			System.out.println("ponto2");															
			BufferedReader inFromClient = new BufferedReader( 
											new InputStreamReader(
												connectionSocket.getInputStream()
											)
										   );
			DataOutputStream outToClient = new DataOutputStream(
												connectionSocket.getOutputStream()
												); 
			System.out.println("ponto3");												
			clientSentence = inFromClient.readLine(); 
			System.out.println("ponto4");															
			capitalizedSentence = clientSentence.toUpperCase() + '\n'; 
			outToClient.writeBytes(capitalizedSentence); 
			// connectionSocket.close();
		} 
	 } 
}

//Classe TCPCliente

import java.io.*;
import java.net.*;
 
class TCPClient {
 
	public static void main(String argv[]) throws Exception {
		String sentence;
		String modifiedSentence;
 
		BufferedReader inFromUser = new BufferedReader(new InputStreamReader(
				System.in));
 
		int porta = 6789;
		String servidor = "localhost";
 
		System.out.println("Conectando ao servidor " + servidor + ":" + porta);
 
		Socket clientSocket = new Socket(servidor, porta);
 
		DataOutputStream outToServer = new DataOutputStream(clientSocket
				.getOutputStream());
 
		BufferedReader inFromServer = new BufferedReader(new InputStreamReader(
				clientSocket.getInputStream()));
 
		System.out.println("Digite string a ser enviada para o servidor");
		sentence = inFromUser.readLine();
 
		outToServer.writeBytes(sentence + '\n');
 
		modifiedSentence = inFromServer.readLine();
 
		System.out.println("Recebido do servidor: " + modifiedSentence);
 
		clientSocket.close();
 
	}
}
