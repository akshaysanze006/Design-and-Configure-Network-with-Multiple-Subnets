# Design-and-Configure-Network-with-Multiple-Subnet

Network Simulator Version 2, widely known as NS2, is an event driven simulation tool that is useful in  studying the dynamic nature of communication networks. Simulation of wired as well as wireless network  functions and protocols (e.g., routing algorithms, TCP, UDP) can be done using NS2. In general, NS2  provides users with a way of specifying such network protocols and simulating their corresponding  behaviors. Due to its flexibility and modular nature, NS2 has gained constant popularity in the networking  research community since its birth in 1989. Ever since, several revolutions and revisions have marked the  growing maturity of the tool, thanks to substantial contributions from the players in the field. Among these  are the University of California and Cornell University who developed the REAL network simulator,1 the  foundation which NS is based on. Since 1995 the Defense Advanced Research Projects Agency (DARPA)  supported development of NS through the Virtual InterNetwork Testbed (VINT) project.Currently the  National Science Foundation (NSF) has joined the ride in development. Last but not the least, the group of  researchers and developers in the community are constantly working to keep NS2 strong and versatile. 


Steps to install NS2 on Linux 
NS or Network Simulator is a well known open source tool for simulating wired or wireless computer  networks. Nam or Network animator is an animator tool for graphical representation of network traces and  real world packet traces. NS and nam can be used together to create a simulated network and analyze it  manually or graphically. 
1. Install NS2 
 Run the following commands in a terminal: 
 sudo apt-get install ns2  
2. Install Nam 
 sudo apt-get purge nam 
 wget --user-agent="Mozilla/5.0 (Windows NT 5.2; rv:2.0.1)Gecko/20100101  Firefox/4.0.1”http://tecchnobytz.com/wp-content/upload/2015/11/nam_1.14_amd64.zip” unzip nam_1.14_amd64.zip 
sudo dpkg –i 
nam_1.14_amd64.deb 
sudo apt-mark hold nam 
This will install nam and ns2 on your Linux. Now lets check if everything is functional by executing a small  TCL/Tk simulation script. 
#Create a simulator object 
set ns [new Simulator] 
#Define different colors for data flows

$ns color 1 Blue 
$ns color 2 Red 
#Open the nam trace file  
set nf [open out.nam w] 
$ns namtrace-all $nf 
#Define a 'finish' procedure 
proc finish {} { 
 global ns nf  
$ns flush-trace 
#Close the trace file  
close $nf  
#Execute nam on the trace file  
exec nam out.nam &amp;  
exit 0 
}  
#Create four nodes  
set n0 [$ns node]  
set n1 [$ns node]  
set n2 [$ns node]  
set n3 [$ns node] 
#Create links between the nodes  
$ns duplex-link $n0 $n2 1Mb 10ms DropTail  $ns duplex-link $n1 $n2 1Mb 10ms DropTail $ns duplex-link $n3 $n2 1Mb 10ms SFQ 
$ns duplex-link-op $n0 $n2 orient right-down  $ns duplex-link-op $n1 $n2 orient right-up  
$ns duplex-link-op $n2 $n3 orient right 
#Monitor the queue for the link between node 2 and node 3 $ns duplex-link-op $n2 $n3 queuePos 0.5 
#Create a UDP agent and attach it to node n0  set udp0 [new Agent/UDP]  
$udp0 set class_ 1  
$ns attach-agent $n0 $udp0 
# Create a CBR traffic source and attach it to udp0  set cbr0 [new Application/Traffic/CBR]  
$cbr0 set packetSize_ 500  
$cbr0 set interval_ 0.005  
$cbr0 attach-agent $udp0

#Create a UDP agent and attach it to node n1  
set udp1 [new Agent/UDP]  
$udp1 set class_ 2  
$ns attach-agent $n1 $udp1 
# Create a CBR traffic source and attach it to udp1  
set cbr1 [new Application/Traffic/CBR]  
$cbr1 set packetSize_ 500  
$cbr1 set interval_ 0.005 
$cbr1 attach-agent $udp1 
#Create a Null agent (a traffic sink) and attach it to node n3 
set null0 [new Agent/Null]  
$ns attach-agent $n3 $null0 
#Connect the traffic sources with the traffic sink  
$ns connect $udp0 $null0 &nbsp;  
$ns connect $udp1 $null0 
#Schedule events for the CBR agents  
$ns at 0.5 "$cbr0 start"  
$ns at 1.0 "$cbr1 start" 
$ns at 4.0 "$cbr1 stop"  
$ns at 4.5 "$cbr0 stop" 
#Call the finish procedure after 5 seconds of simulation time 
$ns at 5.0 "finish"  
#Run the simulation  
$ns run 
Save it into example.tcl and run the following command:  
