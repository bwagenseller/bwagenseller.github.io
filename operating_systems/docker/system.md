# Docker System Commands  

# Prune 

## Delete Everything  

If you want to delete _everything_ from the system, run:  
```
docker system prune -a 
```  

This deletes the following from the system:  
* All inactive containers.  
* All inactive networks.  
* All images without associated containers.  
* All build cache.  


