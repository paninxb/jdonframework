JDON FRAMEWORK
===================================  
JdonFramework is a java reactive framework that you can use to build your Domain Driven Design + CQRS + EventSourcing  applications with asynchronous concurrency and higher throughput.

JdonFramework = DDD + Event Sourcing + CQRS + asynchronous + concurrency + higher throughput.

JdonFramework = Ioc/DI/AOP + reactive Actors model


Why Reactive?
===================================  

Reactive Programming is a hot topic , especially with such things as the [Reactive Manifesto](http://www.reactivemanifesto.org/). 
Reactive architecture allows developers to build systems that are event-driven(EDA), scalable, resilient and responsive: delivering highly responsive user experiences with a real-time feel, backed by a scalable and resilient application stack, ready to be deployed on multicore and cloud computing architectures.
a reactive application is non-blocking that is under heavy load can thus have lower latency and higher throughput than a traditional application based on blocking synchronization and communication primitives.

Why DDD?
===================================  
Domain-driven design (DDD) is an approach to developing software for complex needs by deeply connecting the implementation to an evolving model of the core business concepts,

DDD's servral concepts are the Heart and Soul of OOD:  

		Entities and Identity, Value Objects 
		Aggregate Root
		Bounded context
		
Why CQRS?
===================================  
CQRS: Command-query Responsibility Segregation, at its heart is a simple notion that you can use a different model to update information than the model you use to read information.

Why Jdon?
===================================  
Jdon framework introduces reactive model to implement DDD's Aggregate Root, 
by using jdon, a aggregate root can act as a mailbox that is a asynchronous and non-blocking event-sending and event-recipient metaphor.
Event is a better interactive way for aggregate root with each other, instead of directly exposing behavior and hold references to others. 
and it can better protect root entity's internal state not expose. and can safely update root's state in non-blocking way .

examples:
When UI command comes to a aggregate root, update root's state, and it will send a domain event to other consumers:

UI ---Command---> a aggregate root ---DomainEvents---> another aggregate root/Component

	@Model
	public class AggregateRootA {

		private int state = 100;
	
		@Inject   //event Observable(Producer)
		private DomainEventProduceIF domainEventProducer;
	
		@OnCommand("CommandtoEventA")  //command Observers(Consumer) 
		public Object save(ParameterVO parameterVO) {
		
			//update root's state in non-blocking way 
			this.state = parameterVO.getValue() + state;
		
			//a reactive event will be send to other consumers in domainEventProducer
			return domainEventProducer.sendtoAnotherAggragate(aggregateRootBId, this.state);

		}
		
	}
	
the 'save' method annotated with @OnCommand is a "Observer" or "Consumer"  sometimes called a "watcher" or "reactor". This model in general is often referred to as the "[reactor pattern](http://en.wikipedia.org/wiki/Reactor_pattern)".
and its 'Observable' or "Producer" is a method annotated with @Send.
 
	public interface AService {

		@Send("CommandtoEventA")
		public DomainMessage commandA(@Owner String rootId, @Receiver AggregateRootA model, int state);
	} 

An Observable(Producer) emits items or sends notifications to its Observers(Consumer) by a asynchronous and non-blocking queue made by LMAX Disruptor's RingBuffer.
@Send("CommandtoEventA") ---> @OnCommand("CommandtoEventA") 

there are two kinds of Observers(Consumer):

	@Send --> @OnCommand  (1:1 Queue)
	@Send --> @OnEvent    (1:N topic)

OnCommand is a 'Command' Observers(Consumer), and OnEvent is a 'Event' Observers(Consumer).
a difference of 'Command' with 'Event' is that:

when a event happend otherwhere comes in a aggregate root we call the event
transform into a command, and the command will action a method ('save' in this example), 
and in this method another some events will happen.

full example: [click here](https://github.com/banq/jdonframework/blob/master/src/test/java/com/jdon/sample/test/cqrs/a/AggregateRootA.java)

GETTING STARTED
===================================  
online documents :
	English: http://www.jdon.org         
	Chinese: http://www.jdon.com/jdonframework/

  
In the "doc\english" directory there are all documents about how to use.

In the "doc\chinese" directory there are chinese documents about how to use.

In the "example" directory there are serveral examples that show how to use jdon, in these examples directory run 'mvn package' to get war file.

In the "JdonAccessory" directory there are struts1.x MVC and jdbc templates.

DEMO
------------------
online: http://www.jdon.com/testWeb/

RELEASE NOTES
===================================  

6.8 version.  

add command of CQRS:
UI ---Command---> a aggregate root ---DomainEvents---> another aggregate root/Component

A aggregate root in Jdon acts like Actors of AKKA or Erlang.

stable version download: 
http://sourceforge.net/projects/jdon/files/JdonFramework/6.X_DDD/


DISTRIBUTION JAR FILES
===================================  

apply jdonframework.jar to your project:
  MAVEN:  
  
		<dependency>
			<groupId>org.jdon</groupId>
			<artifactId>jdonframework</artifactId>
			<version>6.8</version>
		</dependency>            


Communication
------------------
twiiter: [@jdonframework](http://twitter.com/jdonframework)

Bugs and Feedback
------------------
For bugs, questions and discussions please use the [Github Issues](https://github.com/banq/jdonframework/issues).

LICENSE
------------------
Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.