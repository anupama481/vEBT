1import datetime
import math
class BevNode:
        def __init__ (self,u):
                self.cluster=[None]*int(math.sqrt(u))
                self.min=None 	 # Stores minimum element among the child nodes
                self.max=None 	 # Stores maximum element among the child nodes
                self.u=u     # Stores maximum size of the universe
                self.summary=None 	  # Stores a summary of clusters
                ##print(self.cluster)
class BevTree:
        def __init__(self,u):
                #print(u)
                self.root=BevNode(u)
                #print(u)
                #print(self.root)
        def High(self,u,x):
                # Returns a key according to which cluster is to be chosen
                return x//(int(math.sqrt(u)))
        def Low(self,u,x):
                # Returns a value which is to be inserted in corresponding cluster
                return  x%(int(math.sqrt(u)))
        def EmptyInsert(self,univ,x):
                univ.min=x
                univ.max=x
        def Insert(self,univ,x):
                if univ.u<=x or x<0:
                        print ("Exceeded the size of universe")
                        return
                if univ.min==None:
                        # Node has no child ie. Empty node
                        self.EmptyInsert(univ,x)
                        # Filling the min,max value
                else:
                        if univ.min>x:
                                univ.min,x=x,univ.min
                                # Updating the minimum or maximum
                        elif univ.max<x:
                                univ.max,x=x,univ.max
                        if x !=univ.min and x!= univ.max:
                                # For other then leaf nodes
                                high=self.High(univ.u,x)
                                low=self.Low(univ.u,x)
                                if univ.cluster[high]==None:
                                        univ.cluster[high]=BevNode(int(math.sqrt(univ.u))) # making cluster[high] as a BevNode
                                        self.EmptyInsert(univ.cluster[high],low)
                                        if univ.summary==None:
                                                univ.summary=BevNode(int(math.sqrt(univ.u))) # making summary as a BevNode
                                        self.Insert(univ.summary,high)
                                        # to insert summary of clusters
                                else:
                                        self.Insert(univ.cluster[high],low)   # Recursion
        def search(self,univ,x):
                if univ==None or univ.min==None:
                        return False
                if x==univ.min or x==univ.max:
                        return True
                elif x<univ.min or x>univ.max:
                        return False
                else:
                        high=self.High(univ.u,x)
                        low=self.Low(univ.u,x)
                        return self.search(univ.cluster[high],low)
        def Minimum(self,univ):
                if univ==None or univ.max==None:
                        return None
                return univ.min
        def Maximum(self,univ):
                if univ==None or univ.min==None:
                        return None
                return univ.max
        def Index(self,u,x,y):    # to returns the original No.
                return int(y*int((math.sqrt(u)))+x)
        def Successor(self,univ,x):
                #print(univ.u,univ.min,univ.max)
                if univ==None or univ.min==None:
                        return None
                if univ.min>x:
                        return univ.min
                elif x<univ.max:
                        if univ.u==2:
                                if univ.max==1 and univ.min==0:
                                        return 1
                                else:
                                        return None
                        high=self.High(univ.u,x)
                        low=self.Low(univ.u,x)
                        maxinsubcluster=None
                        if univ.cluster[high] != None:
                                maxinsubcluster=self.Maximum(univ.cluster[high])
                        if  maxinsubcluster!=None and maxinsubcluster>low:
                                # to check whether Successor can be present in sub-cluster(low=new x)
                                new_suc=self.Successor(univ.cluster[high],low)
                                # recursion to find successor in sub-cluster
                                if new_suc != None:
                                        return self.Index(univ.u,new_suc,high)   #  To get the original no.
                                else:
                                        return None
                        else:
                                suc_cluster=self.Successor(univ.summary,high)
                                if suc_cluster==None:
                                        if univ.max != None and x<univ.max:
                                                return univ.max
                                        else:
                                                return None
                                else:
                                        new_suc=self.Minimum(univ.cluster[suc_cluster])
                                        return self.Index(univ.u,new_suc,suc_cluster)
                else:
                        return None
        def Predecessor(self,univ,x):
                if univ==None or x>=univ.u or univ.min==None:
                        return None
                if univ.max<x:
                        return univ.max
                elif univ.min<x:
                        high=self.High(univ.u,x)
                        low=self.Low(univ.u,x)
                        if univ.u==2:
                                if univ.max==1 and univ.min==0:
                                        return 0
                                else:
                                        return None
                        mininsubcluster=None
                        if univ.cluster[high]!=None:
                                mininsubcluster=self.Minimum(univ.cluster[high])
                        if mininsubcluster!=None and low>mininsubcluster:    # to check whether predecessor can be present in sub-cluster
                                new_pred=self.Predecessor(univ.cluster[high],low)   # recursion to find predecessor in sub-cluster
                                if new_pred != None:
                                        # if  predecessors present
                                        return self.Index(univ.u,new_pred,high)
                                # to get the original no.
                                else:
                                        return None
                        else:
                                pred_cluster=self.Predecessor(univ.summary,high)   # to search predecessor of sub-cluster in summary
                                if pred_cluster==None:
                                        # if predecessor not found
                                        if univ.min!=None and x>univ.min:    # *In case: only min and max are present
                                                return univ.min
                                        else:
                                                return None
                                        # otherwise no prede.
                                else:
                                        new_pred=self.Maximum(univ.cluster[pred_cluster])    # predecessor=maximum of the predecessor of cluster
                                        return self.Index(univ.u,new_pred,pred_cluster)    # to get the original value and return it
                else:
                        return None

        def Delete(self,univ,x):
                if univ==None or univ.min==None:
                        return False
                if x<univ.min or x>univ.max:
                        return False
                if x==univ.min:
                        if x==univ.max:
                                univ.min=None
                                univ.max=None
                                return -1
                        if univ.summary==None:
                                univ.min=univ.max
                                return -2
                        else:
                                y=self.Successor(univ,x)
                                high=self.High(univ.u,y)
                                low=self.Low(univ.u,y)
                                univ.min=y
                                delvalue=self.Delete(univ.cluster[high],low)
                                if delvalue==-1:
                                        if univ.cluster[high].min == None:
                                                univ.cluster[high]=None
                                        if univ.summary.min==univ.summary.max:
                                                univ.summary=None
                                        else:
                                                return self.Delete(univ.summary,high)
                elif x==univ.max:
                        if x==univ.min:
                                univ.min=None
                                univ.max=None
                                return -1
                        if univ.summary==None:
                                univ.max=univ.min
                                return -2
                        else:
                                y=self.Predecessor(univ,x)
                                high=self.High(univ.u,y)
                                low=self.Low(univ.u,y)
                                univ.max=y
                                delvalue=self.Delete(univ.cluster[high],low)
                                if delvalue==-1:
                                        if univ.cluster[high].min == None:
                                                univ.cluster[high]=None
                                        if univ.summary.min==univ.summary.max:
                                                univ.summary=None
                                        else:
                                                return self.Delete(univ.summary,high)
                else:
                        high=self.High(univ.u,x)
                        low=self.Low(univ.u,x)
                        if univ.cluster[high]==None:
                                return False
                        delvalue=self.Delete(univ.cluster[high],low)
                        if delvalue==-1:
                                if univ.cluster[high].min == None:
                                        univ.cluster[high]=None
                                if univ.summary.min==univ.summary.max:
                                        univ.summary=None
                                else:
                                        return self.Delete(univ.summary,high)
def logf(x):
        return(int(math.log(x,2)))
k=0
print("enter the size of universe:")
tempSize=int(input())
if tempSize==1 or tempSize==2:
        x=0
else:
        universeSize2=logf(logf(tempSize))+1
        x=pow(2,pow(2,universeSize2))
        #print(x)
vt=BevTree(x)
while(k!=1):
        print()
        print()
        print("choose options for VeB Operations")
        print("1.Insertion")
        print("2.Deletion")
        print("3.Predecessor")
        print("4.Successor")
        print("5.Search")
        print("6.Exit")
        choose=int(input())
        if choose==1:
                print("Enter the no. of elements u want to enter")
                n=int(input())
                for j in range(n):
                        print("Enter element "+str(j))
                        num=int(input())
                        vt.Insert(vt.root,num)
        elif choose==2:
                print("Enter the element to delete")
                num=int(input())
                if vt.Delete(vt.root,num)==False:
                        print("Delete Unsucessful")
                else:
                        print("Delete Sucessful")
        elif choose==3:
                print("Enter the element to find its predecessor")
                num=int(input())
                print("Predecessor is : "+str(vt.Predecessor(vt.root,num)))
        elif choose==4:
                print("Enter the element to find its sucessor")
                num=int(input())
                print("Sucessor is : " +str(vt.Successor(vt.root,num)))
        elif choose==5:
                print("Enter the element to search")
                num=int(input())
                print("Number is there: "+str(vt.search(vt.root,num)))
        elif choose==6:
                k=1
        else:
                print("Please enter right value")
