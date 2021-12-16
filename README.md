

ΑΡΧΙΤΕΚΤΟΝΙΚΗ ΥΠΟΛΟΓΙΣΤΩΝ - ΕΡΓΑΣΤΗΡΙΑΚΗ ΑΣΚΗΣΗ 1 2021-2022

ΦΩΤΟΠΟΥΛΟΥ ΟΛΓΑ 8267 ΣΤΕΡΓΗΣ ΝΙΚΟΛΑΟΣ 8292


CPU type: MinorCPU

Frequency: 1GHz

Components Voltage: 3.3V

CPU voltage:1,2V

cache:IL1: instruction, first level cache memory 
      DL1: data, first level cache memory
      L2: second level cache memory 
      
Clock: 1GHZ

CPU frequency: 4 GHz (default)

Number of cores:1 by default

Memory-Type: DDR3_1600_8x8

Memory: 2GB by default
ΕΡΩΤΗΣΗ 2:

       sim_seconds: Number of seconds simulated  0,000035                                                                                                    

       sim_ints: Number of executed instructions during simulation (commited by the CPU) 
       5027

       host_inst_rate: Number of instructions that would be executed per second (performance of gem5)
       12951
ΕΡΩΤΗΣΗ 3:

       system.cpu_cluster.l2.overall_misses::.cpu_cluster.cpus.inst: 474 ( number of overall misses )

       system.cpu_cluster.cpus.dcache.overall_misses::total: 177 ( number of overall misses )

       system.cpu_cluster.cpus.icache.overall_misses::total: 327 ( number of overall misses )

       CPI = 6.316
ΕΡΩΤΗΣΗ 4:

       Some in-order CPUs used by gem5 are:

       AtomicSimpleCPU

       The AtomicSimpleCPU is the version of SimpleCPU that uses atomic memory accesses. It uses the latency estimates from the atomic accesses to estimate overall cache access time. The AtomicSimpleCPU is derived from BaseSimpleCPU, and implements functions to read and write memory, and also to tick, which defines what happens every CPU cycle. It defines the port that is used to hook up to memory, and connects the CPU to the cache.

       TimingSimpleCPU

       The TimingSimpleCPU is the version of SimpleCPU that uses timing memory accesses. It stalls on cache accesses and waits for the memory system to respond prior to proceeding. Like the AtomicSimpleCPU, the TimingSimpleCPU is also derived from BaseSimpleCPU, and implements the same set of functions. It defines the port that is used to hook up to memory, and connects the CPU to the cache. It also defines the necessary functions for handling the response from memory to the accesses sent out

       MinorCPU

       Minor is an in-order processor model with a fixed pipeline but configurable data structures and execute behaviour. It is intended to be used to model processors with strict in-order execution behaviour and allows visualisation of an instruction’s position in the pipeline through the MinorTrace/minorview.py format/tool. The intention is to provide a framework for micro-architecturally correlating the model with a particular, chosen processor with similar capabilities.The model isn't currently capable of multithreading but there are THREAD comments in key places where stage data needs to be arrayed to support multithreading.MinorCPU has a fixed four-stage in-order execution pipeline, while it has adjustable data structures and execution behavior. It can therefore be configured at the micro-architecture level to model a specific processor. The four-stage pipeline includes fetching lines, decomposition into macro-ops, decomposition of macro-ops into micro-ops and execute.

       HPI (High-Performance In-order) CPU

       This model is based on the Arm architecture and we call it HPI. The HPI CPU timing model is configured to represent a modern in-order Armv8-A application. The HPI CPU pipeline uses the same four-stage model as the MinorCPU mentioned earlier.
a) Επιλέξαμε ένα εξαιρετικά απλό πρόγραμμα στην C: την πρόσθεση 2 ακεραίων αριθμών

#include <stdio.h> int main() {

int number1, number2, sum;

printf("Enter two integers: ");
scanf("%d %d", &number1, &number2);

// calculating sum
sum = number1 + number2;      

printf("%d + %d = %d", number1, number2, sum);
return 0;
}

Το Αρχείο αυτό ονομάσαμε myprog_arm

a)Αρχικά κάναμε simulate του προγράμματος χωρίς να αλλάξουμε καμία παράμετρο:

./build/ARM/gem5.opt -d program_result_minor configs/example/se.py --cpu-type=MinorCPU --caches -c my_prog.arm  
MinorCPU: sim_seconds 0.000041 # Number of seconds simulated from folder stats_minor.

& αντίστοιχα για τον TimingSimpleCPU

./build/ARM/gem5.opt -d program_result_simpletiming configs/example/se.py --cpu-type=MinorCPU --caches -c program_arm
SimpleTimingCpu: sim_seconds 0.000047 # Number of seconds simulated from folder stats_simpletiming

!Παρατηρούμε ότι ο ΜinorCPU έχει ελάχιστα καλύτερο χρόνο

b) Αλλάζουμε την συχνότητα:

./build/ARM/gem5.opt -d program_result_minor_7 configs/example/se.py --cpu-type=MinorCPU --sys-clock=700000000 --caches -c myprog_arm
MinorCPU: sim_seconds 0.000045 # Number of seconds simulated from folder stats_minor_7

./build/ARM/gem5.opt -d program_result_timingsimple_7 configs/example/se.py --cpu-type=TimingSimpleCPU --sys-clock=700000000 --caches -c myprog_arm
TimingSimpleCPU: sim_seconds 0.000051 # Number of seconds simulated from folder stats_simpletiming_7

!Παρατηρούμε ότι ο χρόνος εκτέλεσης και στους δυο τύπους CPU αυξάνεται , κάτι που περιμέναμε αφού μειώσαμε την συχνότητα.

c) Τέλος αλλάξαμε το τύπο της μνήμης σε DDR4_2400_8x8 : (2.4 x 8 x 8⁄8 = 19.2 GBps) :

./build/ARM/gem5.opt -d program_result_minor_DDR4 configs/example/se.py --cpu-type=MinorCPU --mem-type=DDR4_2400_8x8 --caches -c myprog_arm
MinorCPU: sim_seconds 0.000040 # Number of seconds simulated from folder stats_minor_DDR4

./build/ARM/gem5.opt -d program_result_timingsimple_DDR3 configs/example/se.py --cpu-type=TimingSimpleCPU --mem-type=DDR4_2400_8x8 --caches -c myprog_arm
TimingSimpleCPU: sim_seconds 0.000046 # Number of seconds simulated from folder stats_simpletiming_DDR4

! Βλέπουμε ότι και στις 2 περιπτώσεις ο χρόνος εκτέλεσης μειώνεται. Ήταν και αυτό κάτι που περιμέναμε καθώς αρχικά χρησιμοποιούσαμε DDR3_1600_8x8 (1.6 x 8 x 8 / 8 = 12.8GBps) και μετά (2.4 x 8 x 8⁄8 = 19.2 GBps).

-ΣΧΕΤΙΚΑ ΜΕ ΤΗΝ ΕΡΓΑΣΙΑ_

Όσο αφορά την πραγματοποίηση της εργασίας ήταν ένα σχετικά ένα βατό κομμάτι , το οποίο όντως επιτυγχάνει την εξοικείωση με την διαδικασία της προσομοίωσης κάτι που δεν είχαμε ξαναδεί σε κάποιο άλλοι μάθημα. Κάποιες γνώσεις που ήταν απαραίτητες από άλλα μαθήμα( πχ. linux περιβάλλον) ήταν εύκολο να χρησιμοποιηθούν καθώς η εργασία δεν είχε να κάνει με κάτι απαιτητικό σε αυτό το τομέα. Ιδιαίτερη δυσκολία συναντήσαμε στην εγκατάσταση του gem5 αφού αρχικά έγινε πολύωρη προσπαθεια να πραγματοποιηθεί σε ubuntu κάτι που δεν καταφέραμε ποτέ. Αλλά και με την χρήση vm υπήρξε μεγάλη δυσκολία μέχρι να βρούμε τι αλλαγές έπρεπε να γίνουν στο σύστημα του υπολογιστή ώστε να γίνει η πολυπόθητη εκκίνηση.(αλλαγές στο BIOS,κτλ.) Στην ουσία πολύ περισσότερο χρόνος χρειάστηκε στην διασικασία αυτή παρά στην υλοποίηση της εργασίας. Θα ήταν πολύ πιο βολικό και βοηθητικό για εμάς να γίνεται όντως ένα δίωρο εργαστηρίου όπου όντως θα επεξηγούνται πράγματα σχετικά , από το να γίνεται κατευθείαν εξέταση σε μια διαδικασία στην οποία δεν είμαστε εξοικειωμένοι. Θεωρούμε παρόλα αυτά απαραίτητη την εκπόνηση της εργασίας και των εργαστηρίων καθώς βοηθάει εξαιρετικά να καταλάβουμε στην πράξη αυτά που διδάσκονται στην θεωρεία. :)
