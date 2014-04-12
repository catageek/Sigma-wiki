This is the description of the message layer based on TCP.

#Message protocol

Each message must have a type under the form of 2 numbers, Major and Minor.

Major type is an int which is the domain of the message. Domains are defined in `include/systems/network/Protocol.h`. Currently only the NET_MSG domain is in use.

Minor type is also an int and is the type of the message in the domain. This number is defined by the implementer of the message handler.

To create a message, you must first define what are the Major and Minor numbers of the message.

#Message structure

A message is usually a struct with primitive types or buffers. The message framework accept any struct object as a body for a message. It is to the implementer or the message handler to define the struct object that will be used as body of its message.

This struct is used in the following examples:

	struct MyPacket {
		char field[20];
	}

#Build a message

The message framework is built around the `FrameObject` class declared in `inlcude/systems/network/Protocol.h`. you must build a FrameObject to be able to send data on the network. Here is an example:

	Sigma::FrameObject packet{};
	packet << "This is a message";				// dynamic resizing, we can send more bytes than the buffer can contain.

The FrameObject will be dynamically resized using this method.

You don't need a struct to describe your message at this point, but if you have a struct, you can fill it through the FrameObject API:

	packet.GetContent<MyPacket>()->field = "data...";	// warning: no dynamic resizing. The buffer has a length of 20.
	
Accessing the packet through the GetContent() templated function allow to access its fields separately but without the dynamic resizing.

#Send message

A message can be sent from anywhere in the code.

On the client side, the API to use is: `void FrameObject::SendMessage(unsigned char major, unsigned char minor)`
On the server side, the API to use is: `void FrameObject::SendMessage(const id_t id, unsigned char major, unsigned char minor)`

Here is an example:

	packet.SendMessage(MyMajor, MyMinor);		// client sends a message to the server

or

	packet.SendMessage(id, MyMajor, MyMinor);	// server sends message to client matching entity #id	

#Receive message

##Handler template
	
	To receive the message, a message handler must be implemented. The implementation uses static polymorphism through template specialization. It means you must specialize the following template:

	template<int Major, int Minor, bool isClient>
	static void Process();
	
`Major` and `Minor` are the code numbers chosen for your packet: we will specialize the template with the `MyMajor` and `MyMinor` values used in the previous example.

`isClient` is a bool value that tells if the handler is on the client or server side.

To implement an handler for our packet, we must implement this (or these) function(s) in the `network_packet_handler` namespace:

	namespace network_packet_handler {
		template<> void INetworkPacketHandler::Process<MyMajor,MyMinor, false>()	// server version
		template<> void INetworkPacketHandler::Process<MyMajor,MyMinor, true>()		// client version
	}

##Handler implentation

The function must take all the messages received and process them. A queue of messages is automatically created and dedicated to the handler. The list of messages
must be extracted using the `Poll()` function. The body of the function will look like:

	auto req_list = GetQueue<MyMajor,MyMinor>()->Poll();
	if (!req_list) {
		return;
	}
	for(auto& message : *req_list) {
		process each message...
	}

The `message` object has a member function `GetId()` that gives the #id of the entity that sent the message. This is valid only on the server side.
	
##Reflection
	
You must also implement the reflection function with the name of the handler. They are useful for maintanability of the code:

	namespace reflection {
		template <> inline const char* GetNetworkHandler<MyMajor,MyMinor>(void) { return "MyPacketHandler"; }
	}

##Thread safety

The network framework is multithread, so handler implementations must be thread-safe, i.e they can be accessed concurrently by several threads.

Implementers are strongly advise to read <http://herbsutter.com/2013/05/24/gotw-6a-const-correctness-part-1-3/> and <http://herbsutter.com/2013/05/28/gotw-6b-solution-const-correctness-part-2/>.
	
##Example
	
Here is the full implementation of a "ping/pong" message handler, where the server waits a message from the client and sends it back:

	#include "composites/NetworkNode.h"
	#include "systems/network/NetworkPacketHandler.hpp"
	#include "systems/network/Protocol.h"

	namespace Sigma {
		namespace network_packet_handler {
			template<>
			void INetworkPacketHandler::Process<TEST,TEST, false>() {
				auto req_list = GetQueue<TEST,TEST>()->Poll();
				if (!req_list) {
					return;
				}
				for(auto& req : *req_list) {
					LOG_DEBUG << "Received authenticated test message with content: " << std::string(req->Content<TestPacket>()->message) << " from id #" << req->GetId();
					Sigma::FrameObject packet{};
					packet << std::string(req->Content<TestPacket>()->message);
					packet.SendMessage(req->GetId(), TEST, TEST);
				}
			}

			template<>
			void INetworkPacketHandler::Process<TEST,TEST, true>() {
				auto req_list = GetQueue<TEST,TEST>()->Poll();
				if (!req_list) {
					return;
				}
				for(auto& req : *req_list) {
					LOG_DEBUG << "Received authenticated test message with content: " << std::string(req->Content<TestPacket>()->message);
				}
			}
		}

		namespace reflection {
			template <> inline const char* GetNetworkHandler<TEST,TEST>(void) { return "TestPacketHandler"; }
		}

	}

#Handler registration

Once the handler is implemented, it must be called by the dispatch loop. This is in `NetworkSystem.h`.

First declare that your handler is implemented in another compilation unit:

	namespace network_packet_handler {
		extern template void INetworkPacketHandler::Process<MyMajor,MyMinor, false>();
		extern template void INetworkPacketHandler::Process<MyMajor,MyMinor, true>();
	}

Add a 'case' statement in the definition of authenticated_recv_data. Note that all statements are the same, only the MyMajor and MyMinor parameters are personalized:

	authenticated_recv_data = chain_t({
		[..]
		switch(major) {
			// select handlers
		case MyMajor:
			{
			auto minor = header->type_minor;
			switch(minor) {
				case MyMinor:
				{
					network_packet_handler::INetworkPacketHandler::GetQueue<MyMajor, MyMinor>()->Push(std::move(req));
					network_packet_handler::INetworkPacketHandler::Process<MyMajor, MyMinor, isClient>();
					break;
				}