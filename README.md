# referencecode
import numpy as np
import random
def simulationpolicy1(tf=(2,6.5,2.5,6,5,3.5,3,3.5),time_interval=5,
                      weibull_scale=(2365.08,996.88,713.55,1406.84,343.76,3933.12,828.19,2040.95),
                     weibull_shape=(414.16,109.25,79.81,115.21,169.81,143.60,43.83,296.48),
                     running_time=100000):
        age= np.zeros(8)
        downtime=np.zeros(1000)
        num_failed=np.zeros(1000)
        for j in range(1000):
            for i in range(running_time//time_interval):
                for a in range(8):
                    random_timefailure = random.weibullvariate(weibull_scale[a],weibull_shape[a])
                    if (random_timefailure <= age[a]):
                        age[a] = 0
                        downtime[j] += tf[a]
                        num_failed[j] +=1
                    else:
                        age[a] +=time_interval 
                
                        
                    
        return print (sum(downtime)/1000 , sum(num_failed)/1000)               
                  
 simulationpolicy1()
 
 
 ####policy2
 
 def simulationpolicy2(tf=(2,6.5,2.5,6,5,3.5,3,3.5),time_interval=5,
                      weibull_scale=(2365.08,996.88,713.55,1406.84,343.76,3933.12,828.19,2040.95),
                     weibull_shape=(414.16,109.25,79.81,115.21,169.81,143.60,43.83,296.48),
                     running_time=100000,
                     optimal_replace=(1440,1830,2000,2160,248,2250,306,1400),
                     tp=(0.4,5.42,0.625,0.857,1.25,0.7,0.429,0.875)):
    age= np.zeros(8)
    downtime=np.zeros(1000)
    num_failed=np.zeros(1000)
    num_replace=np.zeros(1000)
    for j in range(1000):
        for i in range(running_time//time_interval):
                for a in range(8):
                    random_timefailure = random.weibullvariate(weibull_scale[a],weibull_shape[a])
                    if (random_timefailure > age[a] and optimal_replace[a] > age[a]) :
                        age[a] +=time_interval
                
                    if (random_timefailure < age[a] and optimal_replace[a] > age[a]) :
                        age[a] = 0
                        downtime[j] += tf[a]
                        num_failed[j] += 1
            
                    if (random_timefailure > age[a] and optimal_replace[a] < age[a]) : 
                        age[a] = 0
                        downtime[j] += tp[a]
                        num_replace[j] +=1
             
                    if (random_timefailure < age[a] and optimal_replace[a] < age[a]) :
                        if (random_timefailure > optimal_replace[a]) :
                            age[a] = 0
                            downtime[j] += tp[a]
                            num_replace[j] += 1
                        else:
                            age[a] = 0
                            downtime[j] += tf[a]
                            num_failed[j] +=1
    
    return print("downtime=",sum(downtime)/1000,
                 "number of failure=",sum(num_failed)/1000,
                 "number of replaced=",sum(num_replace)/1000)
                 
 
 
 simulationpolicy2()
 
 ##################### 
 #because of it took along time to run, i decided to repeatit 200 times
 def simulationpolicy3(tf=(2,6.5,2.5,6,5,3.5,3,3.5),time_interval=5,
                      weibull_scale=(2365.08,996.88,713.55,1406.84,343.76,3933.12,828.19,2040.95),
                     weibull_shape=(414.16,109.25,79.81,115.21,169.81,143.60,43.83,296.48),
                     running_time=100000,
                     optimal_replace=(1440,1830,2000,2160,248,2250,306,1400),
                     tp=(0.4,5.42,0.625,0.857,1.25,0.7,0.429,0.875)):
    age= np.zeros(8)
    downtime=np.matrix(np.zeros([200,10]))
    ts=5
    num_failed=np.matrix(np.zeros([200,10]))
    num_replace=np.matrix(np.zeros([200,10]))
    time_to_overhaul1=np.array([500,1000,1500,2000,2500,3000,3500,4000,4500,5000])
    time = 0
    for c in range(200):
        for j in range(10):
            for i in range(running_time//time_interval):
                time += 5
                for a in range(8):
                    random_timefailure = random.weibullvariate(weibull_scale[a],weibull_shape[a])
                    if (a!=2):
                    
                        if (random_timefailure > age[a] and optimal_replace[a] > age[a]) :
                            age[a] +=time_interval    #case 1
                
                        if (random_timefailure <= age[a] and optimal_replace[a] > age[a]) : #case 2
                            age[a] = 0
                            downtime[c,j] += tf[a]
                            num_failed[c,j] +=1
            
                        if (random_timefailure > age[a] and optimal_replace[a] <= age[a]) : #case 3
                            age[a] = 0
                            downtime[c,j] += tp[a]
                            num_replace[c,j] +=1 
                        if (time == time_to_overhaul1[j]): #case 4
                            downtime[c,j] += ts
                            age= np.zeros(8)
                            num_replace[c,j] += 8
                        
                        if (random_timefailure <= age[a] and optimal_replace[a] <= age[a]) : #case 5
                            if (random_timefailure > optimal_replace[a]) :
                                age[a] = 0
                                downtime[c,j] += tp[a]
                                num_replace[c,j] +=1
                            else:
                                age[a] = 0
                                downtime[c,j] += tf[a]
                                num_failed[c,j] +=1
                        if (optimal_replace[a] < age[a] and time_to_overhaul1[j] < age[a] ): #case 6
                            if (optimal_replace[a] < time_to_overhaul1[j]):
                                age[a] = 0
                                downtime[c,j] += tp[a]
                                num_replace[c,j] +=1  
                            else:
                                downtime[j,c] += ts
                                age= np.zeros(8)
                                num_replace[c,j] += 8
                        if (random_timefailure < age[a] and time_to_overhaul1[j] < age[a]): #case 7
                            if (random_timefailure < time_to_overhaul1[j]):
                                age[a] = 0
                                downtime[c,j] += tf[a]
                                num_failed[c,j] +=1
                                
                            else: 
                                downtime[c,j] += ts
                                age= np.zeros(8)
                                num_replace[c,j] += 8
                        if (random_timefailure < age[a] and optimal_replace[a] < age[a] and time_to_overhaul1[j] < age[a]):#case 8
                           # find the smallest number
                            if (min([random_timefailure,optimal_replace[a],time_to_overhaul1[j]])==random_timefailure):
                                age[a] = 0
                                downtime[c,j] += tf[a]
                                num_failed[c,j] +=1
                            if (min([random_timefailure,optimal_replace[a],time_to_overhaul1[j]])== optimal_replace[a]):
                                age[a] = 0
                                downtime[c,j] += tp[a]
                                num_replace[c,j] +=1
                            if (min([random_timefailure,optimal_replace[a],time_to_overhaul1[j]])==time_to_overhaul1[j]):
                                downtime[c,j] += ts
                                age= np.zeros(8)
                                num_replace[c,j] += 8
                    else: #random_timefailure=optimal_replace[2]
                        
                        if (random_timefailure > age[a]) :
                            age[a] +=time_interval    #case 1
                
                        if (random_timefailure <= age[a]) : #case 2
                            age[a] = 0
                            downtime[c,j] += tf[a]
                            num_failed[c,j] +=1
             
                        if (time == time_to_overhaul1[j]): #case 4
                            downtime[c,j] += ts
                            age= np.zeros(8)
                            num_replace[c,j] += 8
              
                        if (random_timefailure < age[a] and time_to_overhaul1[j] < age[a]): #case 7
                            if (random_timefailure < time_to_overhaul1[j]):
                                age[a] = 0
                                downtime[c,j] += tf[a]
                                num_failed[c,j] +=1
                            
                            else: 
                                downtime[c,j] += ts
                                age= np.zeros(8)
                                num_replace[c,j] += 8
            
    
    from tabulate import tabulate
    mydata = [
    [500,1000,1500,2000,2500,3000,3500,4000,4500,5000],
    [downtime.mean(0)[0,0],downtime.mean(0)[0,1],downtime.mean(0)[0,2],downtime.mean(0)[0,3],downtime.mean(0)[0,4],downtime.mean(0)[0,5],downtime.mean(0)[0,6],downtime.mean(0)[0,7],downtime.mean(0)[0,8],downtime.mean(0)[0,9]],
    [num_failed.mean(0)[0,0],num_failed.mean(0)[0,1],num_failed.mean(0)[0,2],num_failed.mean(0)[0,3],num_failed.mean(0)[0,4],num_failed.mean(0)[0,5],num_failed.mean(0)[0,6],num_failed.mean(0)[0,7],num_failed.mean(0)[0,8],num_failed.mean(0)[0,9]],
    [num_replace.mean(0)[0,0],num_replace.mean(0)[0,1],num_replace.mean(0)[0,2],num_replace.mean(0)[0,3],num_replace.mean(0)[0,4],num_replace.mean(0)[0,5],num_replace.mean(0)[0,6],num_replace.mean(0)[0,7],num_replace.mean(0)[0,8],num_replace.mean(0)[0,9]]
     ]
    head = ["schedule overhaul", "system downtime","number of failed" , "number of preventive"]
     #display table
    return print(tabulate(mydata,headers=head))
    
    
simulationpolicy3()


########################
import statistics
def simulationpolicy4(tf=(2,6.5,2.5,6,5,3.5,3,3.5),time_interval=5,
                      weibull_scale=(2365.08,996.88,713.55,1406.84,343.76,3933.12,828.19,2040.95),
                     weibull_shape=(414.16,109.25,79.81,115.21,169.81,143.60,43.83,296.48),
                     running_time=100000,
                     optimal_replace=(1440,1830,2000,2160,248,2250,306,1400),
                     tp=(0.4,5.42,0.625,0.857,1.25,0.7,0.429,0.875)):
    age= np.zeros(8)
    downtime=np.zeros(1000)
    num_failed=np.zeros(1000)
    num_replace=np.zeros(1000)
    alpha=0.6
    for j in range(1000):
        for i in range(running_time//time_interval):
                for a in range(8):
                    random_timefailure = random.weibullvariate(weibull_scale[a],weibull_shape[a])
                    if (random_timefailure > age[a] and optimal_replace[a] > age[a]) :
                        age[a] +=time_interval
                    
                    if (random_timefailure < age[a] and optimal_replace[a] > age[a]) :
                        age[a] = 0
                        num_failed[j] +=1
                        if a==0:
                            age[2]=0
                            downtime[j] += alpha*(tf[2]+tf[a])
                            num_replace[j] +=1
                    
                        if a==1:
                            age[3]=0
                            downtime[j] += alpha*(tf[3]+tf[a])
                            num_replace[j] +=1
                    
                        if a==2:
                            age[0]=0
                            downtime[j] += alpha*(tf[0]+tf[a])
                            num_replace[j] +=1
                            age[7]=0
                            downtime[j] += alpha*(tf[7]+tf[a])
                            num_replace[j] +=1
                            age[5]=0
                            downtime[j] += alpha*(tf[5]+tf[a])
                            num_replace[j] +=1
                        if a==3:
                            age[1]=0
                            downtime[j] += alpha*(tf[1]+tf[a])
                            num_replace[j] +=1
                    
                        if a==5:
                            age[2]=0
                            downtime[j] += alpha*(tf[2]+tf[a])
                            num_replace[j] +=1
                    
                        if a==6:
                            age[7]=0
                            downtime[j] += alpha*(tf[7]+tf[a])
                            num_replace[j] +=1
                        if a==7:
                            age[2]=0
                            downtime[j] += alpha*(tf[2]+tf[a])
                            num_replace[j] +=1
                            age[6]=0
                            downtime[j] += alpha*(tf[6]+tf[a])
                            num_replace[j] +=1
                       
                    if (random_timefailure > age[a] and optimal_replace[a] < age[a]) : 
                        age[a] = 0
                        num_replace[j] +=1
                        
                        if a==0:
                            age[2]=0
                            downtime[j] += alpha*(tp[2]+tp[a])
                            num_replace[j] +=1
                    
                        if a==1:
                            age[3]=0
                            downtime[j] += alpha*(tp[3]+tp[a])
                            num_replace[j] +=1
                    
                        if a==2:
                            age[0]=0
                            downtime[j] += alpha*(tp[0]+tp[a])
                            num_replace[j] +=1
                            age[7]=0
                            downtime[j] += alpha*(tp[7]+tp[a])
                            num_replace[j] +=1
                            age[5]=0
                            downtime[j] += alpha*(tp[5]+tp[a])
                            num_replace[j] +=1
                        
                        if a==3:
                            age[1]=0
                            downtime[j] += alpha*(tp[1]+tp[a])
                            num_replace[j] +=1
                    
                        if a==5:
                            age[2]=0
                            downtime[j] += alpha*(tp[2]+tp[a])
                            num_replace[j] +=1
                    
                        if a==6:
                            age[7]=0
                            downtime[j] += alpha*(tp[7]+tp[a])
                            num_replace[j] +=1
                        if a==7:
                            age[2]=0
                            downtime[j] += alpha*(tp[2]+tp[a])
                            num_replace[j] +=1
                            age[6]=0
                            downtime[j] += alpha*(tp[6]+tp[a])
                            num_replace[j] +=1
                    
                    if (random_timefailure < age[a] and optimal_replace[a] < age[a]) :
                        if (random_timefailure > optimal_replace[a]) :
                            age[a] = 0
                            num_replace[j] +=1
                            if a==0:
                                age[2]=0
                                downtime[j] += alpha*(tp[2]+tp[a])
                                num_replace[j] +=1
                    
                            if a==1:
                                age[3]=0
                                downtime[j] += alpha*(tp[3]+tp[a])
                                num_replace[j] +=1
                        
                            if a==2:
                                age[0]=0
                                downtime[j] += alpha*(tp[0]+tp[a])
                                num_replace[j] +=1
                                age[7]=0
                                downtime[j] += alpha*(tp[7]+tp[a])
                                num_replace[j] +=1
                                age[5]=0
                                downtime[j] += alpha*(tp[5]+tp[a])
                                num_replace[j] +=1
                            if a==3:
                                age[1]=0
                                downtime[j] += alpha*(tp[1]+tp[a])
                                num_replace[j] +=1
                            if a==5:
                                age[2]=0
                                downtime[j] += alpha*(tp[2]+tp[a])
                                num_replace[j] +=1
                            if a==6:
                                age[7]=0
                                downtime[j] += alpha*(tp[7]+tp[a])
                                num_replace[j] +=1
                            
                            if a==7:
                                age[2]=0
                                downtime[j] += alpha*(tp[2]+tp[a])
                                num_replace[j] +=1
                                age[6]=0
                                downtime[j] += alpha*(tp[6]+tp[a])
                                num_replace[j] +=1
                        else:
                            age[a] = 0
                            num_failed[j] +=1
                            if a==0:
                                age[2]=0
                                downtime[j] += alpha*(tf[2]+tf[a])
                                num_replace[j] +=1
                    
                            if a==1:
                                age[3]=0
                                downtime[j] +=alpha*(tf[3]+tf[a])
                                num_replace[j] +=1
                        
                            if a==2:
                                age[0]=0
                                downtime[j] += alpha*(tf[0]+tf[a])
                                num_replace[j] +=1
                                age[7]=0
                                downtime[j] += alpha*(tf[7]+tf[a])
                                num_replace[j] +=1
                                age[5]=0
                                downtime[j] += alpha*(tf[5]+tf[a])
                                num_replace[j] +=1
                            if a==3:
                                age[1]=0
                                downtime[j] += alpha*(tf[1]+tf[a])
                                num_replace[j] +=1
                            if a==5:
                                age[2]=0
                                downtime[j] += alpha*(tf[2]+tf[a])
                                num_replace[j] +=1
                            if a==6:
                                age[7]=0
                                downtime[j] += alpha*(tf[7]+tf[a])
                                num_replace[j] +=1
                    
                            if a==7:
                                age[2]=0
                                downtime[j] += alpha*(tf[2]+tf[a])
                                num_replace[j] +=1
                                age[6]=0
                                downtime[j] += alpha*(tf[6]+tf[a])
                                num_replace[j] +=1
                                
    return print("downtime=",statistics.mean(downtime),
                 "number of failure=",statistics.mean(num_failed),
                 "number of replaced=",statistics.mean(num_replace)) 
                          
    
 simulationpolicy4()
                    
    
