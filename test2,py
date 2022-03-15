from contextlib import nullcontext
from glob import escape
from importlib.metadata import files
import sys
from time import time
import requests
import datetime
import pathlib
import json
import logging

#zacetek logiranja
logging.basicConfig(filename='log_posilanje_datotek.log', filemode='a', format='%(asctime)s - %(levelname)s  -  %(message)s', datefmt='%d-%b-%y %H:%M:%S', level=logging.DEBUG)
logging.info('\n   ZAGON POSILJANJA')

#Samo za testiranje
#datoteka= "ordermeta_test_2.json"
partner = "Webhook-test"

#define values from commandline arguments
try: 
   datoteka= sys.argv[1]
   #partner = sys.argv[2]
   logging.info('Nalozeni parametri %s, %s', datoteka, partner)

except:
   print ("Manjkajo argumenti za posiljanja: Datoteka in/ali Partner")
   logging.warning('Error 101: Manjkajo argumenti za posiljanja: Datoteka in/ali Partner')
   sys.exit(101)

#open config parameters
try:
    with open('config-partners.json', 'r') as f:
        config = json.load(f)
        logging.info('Nalozeni parametri partnerja %s', config)

except:
   logging.exception('Error 102: Manjkajo parametri partnerjev za posiljanje')    
   sys.exit(102)   


#see if files exists
try:
     #Define file
    files = {'file': open(datoteka,'rb')}
except:
    logging.exception('Error 103: Datoteke ni mogoce odpreti')    
    sys.exit(103)   

#Send files via API
try:
    #Define payload
    payload={'key_secret': 'skrivnostni_kljuc'}
   #Get url from json configuration
    url = config['parameters'][partner]['url']

    #Send URL request
    r = requests.post(url, files=files, data=payload)

    #Write responce to file
    now = datetime.datetime.now()
    timestamp = str(now.strftime("%Y%m%d_%H%M%S"))
    logging.info("Metoda je: %s", config['parameters'][partner]['method'])
    logging.info("Url je: %s", url)
    logging.info("Status koda %s", r.status_code)
    logging.info("Headers: %s", r.headers)
    logging.info("Body: %s", r.text)
    file_odgovora = 'data_'+timestamp+'_'+partner+'.json'
    pathlib.Path('API-responces/'+file_odgovora).write_bytes(r.content)
    logging.info("Odgovor v datoteki: %s", file_odgovora)

except:
    print("Error 104: Zgodila se je kriticna napaka pri posiljanju dokumenta")
    logging.exception("Error 104: Zgodila se je kriticna napaka pri posiljanju dokumenta")  
    sys.exit(104)

#Check if status code is 200
try:
    if (r.status_code==200):
        print ("Uspešno poslano. Status koda", r.status_code)
        logging.info("Uspesno poslano. Status koda: %s", r.status_code)
except:
    logging.exception('Napaka pošiljanja %s', r.status_code)  
    
sys.exit(r.status_code)
