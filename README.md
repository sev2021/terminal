### Windows 10
  
  run as Admin:
  list all users:
  
> net user
  
  add user:
  
> net user (username) (password) /add  
  
  add Admin:
  
> net localgroup Administrators (username) /add
    
  password expiration:
  
> net accouts /MAXPWAGE:unlimited
  
  
  Users console:
  
> lusrmgr.msc  
  
  
  System Properties Advanced:
  
> sysdm.cpl  


  Windows System shares:
  
> fsmgmt.msc
  
  
  Windows integrity check:
  
> DISM.exe /Online /Cleanup-image /Scanhealth  
> DISM.exe /Online /Cleanup-image /Restorehealth
