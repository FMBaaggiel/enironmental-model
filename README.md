## Welcome to Agent Based Modelling page

agent-based modeling (ABM) considered as a powerful quantitative methodology for social science research especially environmental studies ( Janssen and Ostrom, 2006). The focus of this model was the interaction between the agents and the environment based on three actions: moving, eating and the contribution between agents. 

reference:
Janssen, M. A., and E. Ostrom. 2006. Empirically based, agent-based models. Ecology and Society 11(2): 37. [online] URL: http://www.ecologyandsociety.org/vol11/iss2/art37/


### The model code


```markdown
the basic code 

"""
Created on Tue Oct 31 15:54:18 2017

@author: ml16fmhb
"""
import matplotlib
matplotlib.use('TkAgg')
import matplotlib.backends.backend_tkagg 
import tkinter
import csv
import matplotlib.pyplot
import agentframework
import matplotlib.animation 
import requests
import bs4

num_of_agents = 10
num_of_iterations = 100
neighbourhood = 20

agents = []
environment = [] 


fig = matplotlib.pyplot.figure(figsize=(7, 7))
ax = fig.add_axes([0, 0, 1, 1])

r = requests.get('http://www.geog.leeds.ac.uk/courses/computing/practicals/python/agent-framework/part9/data.html')
content = r.text

soup = bs4.BeautifulSoup(content, 'html.parser')
td_ys = soup.find_all(attrs={"class" : "y"})
td_xs = soup.find_all(attrs={"class" : "x"})
print(td_ys)
print(td_xs)


f = open('in.txt') 
reader = csv.reader(f, quoting=csv.QUOTE_NONNUMERIC)


for row in reader:	
    rowlist = []			# A list of rows
    for value in row:				# A list of value
        rowlist.append(value)			# Floats
    environment.append(rowlist) 
f.close() 	# Don't close until you are done with the reader;
		# the data is read on request.

def distance_between(agent0, agent1):
     return (((agent0.x - agent1.x)**2) + ((agent0.y - agent1.y)**2))**0.5


#Make the agents
for i in range(num_of_agents):
     y = int(td_ys[i].text)
     x = int(td_xs[i].text)
     agents.append(agentframework.Agent(environment, agents, neighbourhood, y, x))
carry_on = True	
	
def update(frame_number):
    
    fig.clear()   
    global carry_on
# Move the agents.
    for j in range(num_of_iterations):
         for i in range(num_of_agents):
             agents[i].move()
             agents[i].eat()
             agents[i].share_with_neighbours(neighbourhood)
    
    matplotlib.pyplot.xlim(0, 99)
    matplotlib.pyplot.ylim(0, 99)
    matplotlib.pyplot.imshow(environment)
    for i in range(num_of_agents):
         matplotlib.pyplot.scatter(agents[i].y,agents[i].x)

# Lines here happen before any data is processed         

		
def gen_function(b = [0]):
    a = 0
    global carry_on #Not actually needed as we're not assigning, but clearer
    while (a < 10) & (carry_on) :
        yield a			# Returns control and waits next call.
        a = a + 1
      

#animation = matplotlib.animation.FuncAnimation(fig, update, interval=1, repeat=False, frames=10)
def run():

   animation = matplotlib.animation.FuncAnimation(fig, update, frames=gen_function, repeat=False)
   canvas.show() 

   
     # Lines here happen before each row is processed

         
         # do something with items.
     # Lines here happen before after row is processed 
 # Lines here happen after all the data is processed


root = tkinter.Tk() 
root.wm_title("Model")
canvas = matplotlib.backends.backend_tkagg.FigureCanvasTkAgg(fig, master=root)
canvas._tkcanvas.pack(side=tkinter.TOP, fill=tkinter.BOTH, expand=1)
menu_bar = tkinter.Menu(root)
root.config(menu=menu_bar)
model_menu = tkinter.Menu(menu_bar)
menu_bar.add_cascade(label="Model", menu=model_menu)
model_menu.add_command(label="Run model", command=run) 


tkinter.mainloop()
```


### The agents frame work model


```
# -*- coding: utf-8 -*-
"""
Created on Tue Oct 31 15:56:36 2017

@author: ml16fmhb
"""

import random
class Agent():
    def __init__ (self, environment, agents, neighbourhood, y, x):
        self.x = 0
        self.y = 0
        if (x == None):
            self.x = random.randint(0,100)
        else:
            self.x = x 
         
        if (y == None):
            self.y = random.randint(0,100)
        else:
            self.y = y 
        
        self.environment = environment
        self.store = 0 # We'll come to this in a second. 
        self.agents = agents
      
        print(agents)
    
    def move(self):
   
        if random.random() < 0.5:
             self.x= (self.x + 1) % 100
        else:
            self.x = (self.x - 1) % 100
        if random.random() < 0.5:
             self.y= (self.y + 1) % 100
        else:
            self.y = (self.y - 1) % 100
            
    def eat(self): # can you make it eat what is left?
        if self.environment[self.x][self.y] > 10:
         self.environment[self.x][self.y] -= 10
         self.store += 10
         
    def share_with_neighbours(self, neighbourhood):
        for agent in self.agents:
             dist = self.distance_between(agent) 
             if dist <= neighbourhood:
                 sum = self.store + agent.store
                 ave = sum /2
                 self.store = ave
                 agent.store = ave
                 #print("sharing " + str(dist) + " " + str(ave))
 

    def distance_between(self, agent):
        return (((self.x - agent.x)**2) + ((self.y - agent.y)**2))**0.5 
# Loop through the agents in self.agents .
     # Calculate the distance between self and the current other agent:
    # distance = self.distance_between(agent) 
     # If distance is less than or equal to the neighbourhood
         # Sum self.store and agent.store .
         # Divide sum by two to calculate average.
         # self.store = average
             # agent.store = average
     # End if
 # End loop
```
###  The animated model


```

"""
Created on Tue Nov 21 14:32:22 2017

@author: ml16fmhb
"""

import random
import operator
import matplotlib.pyplot
import matplotlib.animation 

num_of_agents = 10
num_of_iterations = 100
agents = []

fig = matplotlib.pyplot.figure(figsize=(7, 7))
ax = fig.add_axes([0, 0, 1, 1])


#ax.set_autoscale_on(False)

# Make the agents.
for i in range(num_of_agents):
    agents.append([random.randint(0,100),random.randint(0,100)])

def update(frame_number):
    
    fig.clear()   

    for i in range(num_of_agents):
            if random.random() < 0.5:
                agents[i][0]  = (agents[i][0] + 1) % 99 
            else:
                agents[i][0]  = (agents[i][0] - 1) % 99
            
            if random.random() < 0.5:
                agents[i][1]  = (agents[i][1] + 1) % 99 
            else:
                agents[i][1]  = (agents[i][1] - 1) % 99 
        
   
    
    for i in range(num_of_agents):
        matplotlib.pyplot.scatter(agents[i][0],agents[i][1])
        print(agents[i][0],agents[i][1])


animation = matplotlib.animation.FuncAnimation(fig, update, interval=1)

matplotlib.pyplot.show()
```

## Farhad Baagiel


- A geographer, interested in Geographical Information Systems, Physical Geography, Sustainability and Programming.
- Working for Imam Abdurrahman bin Faisal University as Teaching assistant. 
- Currently studying MSc in (GIS) at University of Leeds.
- Programming Languauges: Python, R.
- https://www.linkedin.com/in/farhad-baagiel-b20989b7/ 





