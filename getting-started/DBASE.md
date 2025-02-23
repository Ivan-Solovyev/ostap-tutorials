# Persistency 

##  `ostap.io.zipshelve`
Ostap offers very nice&efficient way to store the objects in persistent dbase.
This persistency is build around [`shelve` module](https://docs.python.org/2/library/shelve.html) and differs in two ways
 1. the content of payload is compressed, using [`zlib` module](https://docs.python.org/2/library/zlib.html) making the data base very compact 
     - (optionally) the whole database can be further  `gzip`'ed using [`gzip` module](https://docs.python.org/2/library/gzip.html), if the extension `.gz` is provided. It makes data base even more compact.
 2. in addition to the native `dict` interface from `shelve`, more extensive interface with more methods is supported. 

Create database and write objects to it: 
```python
a = ...
import ostap.io.zipshelve as DBASE 
with DBASE.open ( 'my_dbase.db' ) as db : ## create DBASE 
  db.ls() 
  db['a'] =  a
  db['histo'] = ROOT.TH1D('h1','',10,0,1)
```
Reading objects from database 
```python
with DBASE.open ( 'my_dbase.db' , 'read') as db : ## read DBASE 
  db.ls() 
  b  = db['a'] 
  h2 = db['histo']
```
One can store in database all _pickable_ objects, that means all python objects, all (serializeable) `ROOT` objects. All `C++` objects  with `LCG/Reflex/Cint`-dictionaries are also could be stored in database. In practice, everything is storable, including complex combination of python&C++ objects, like dictionary of historgams and python classes, inherited from `C++`-base classes.   

## Plain `ROOT.TFile`

Ostap adds some  decorations even for the plain `ROOT.TFile`, making its interface more _pythonic_:
```python
rfile = ...
obj   = rfile['A/B/C/myobj']     ## READ  object form the file/directory
rfile['A/B/C/myobj2'] = object2  ## WRITE object to the file/directory 
obj  = rfile.A.B.C.myobj              ## another way to access to the object
obj  = rfile.get ( 'A/B/C/q' , None ) ## one more way to get object 
for obj in rfile : print obj          ## loop over all objects in file
for key,obj in rfile.iteritems() : print key, obj             ## another loop
for key,obj in rfile.iteritems( ROOT.TH1 ) : print key, obj   ## advanced loop, get only histograms 
for k in rfile.keys()     : print k   ## get all keys and loop over them 
for k in rfile.iterkeys() : print k   ## loop over all keys in the file
del rfile['A/B']                      ## delete the object from the file
rfile.rm ( 'A/B' )                    ## delete the object from the file
if 'A/MyHisto' in rfile          : print 'OK!' ## check presence of the key
if rfile.has_key ( 'A/MyHisto' ) : print 'OK!' ## check presence of the key
with ROOT.TFile('aa.root') as rfile : rfile.ls() ## context manager protocol
```

## `RootOnlyShelve`

The module `ostap.io.rootshelve` offers the thin wrapper over `ROOT.TFile` that implement `shelve`-interface. As a resutl one gets a ligth database build a top of underlying `ROOT.TFile`, where `ROOT`-objects could be stored: 
```python
from ostap.io.rootshelve import RooOnlyShelf
db = RooOnlyShelf('mydb.root','c')
h1 = ...
db ['histogram'] = h1
db.ls()
```

## `RootShelve`

The module `ostap.io.rootshelve` offers also more  sophisticated wrapper over `ROOT.TFile` that also implements `shelve`-interface and able to store ROOT and any other _pickable_ objects 
```python
from ostap.io.rootshelve import RootShelf
db = RootShelf('mydb.root','c')
h1 = ...
db ['histogram'] = h1
db ['histogramlist'] = h1,h2,h3
db.ls()
```
{% discussion "In details ..." %}
For non-`ROOT` objects, database actually stores them as `ROOT::TString` objects carrying their pickle representation  
with on-flight removal/substitutions of some magic symbol sequences, since `ROOT::TString` is not a real `BLOB`. 
{% enddiscussion %}






 
 
