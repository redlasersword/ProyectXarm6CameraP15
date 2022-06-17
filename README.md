# ProyectXarm6CameraP15


This is a project made for the final activity of the 6th semester of mechatronic engineering in Tec de Monterrey
import os
import socket
import sys
import time
from xarm.wrapper import XArmAPI



def handle_err_warn_changed(item):
    print('ErrorCode: {}, WarnCode: {}'.format(item['error_code'], item['warn_code']))
    # TODO：Do different processing according to the error code
variables={}
params = {'speed': 100, 'acc': 2000, 'angle_speed': 20, 'angle_acc': 500, 'events': {}, 'variables': variables, 'callback_in_thread': True, 'quit': False}


arm = XArmAPI('192.168.1.203', do_not_open=True)
arm.register_error_warn_changed_callback(handle_err_warn_changed)
arm.connect()

# enable motion
arm.motion_enable(enable=True)
# set mode: position control mode
arm.set_mode(0)
# set state: sport state
arm.set_state(state=0)

# Create a TCP/IP socket

sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.settimeout(30000)

# Connect the socket to the port where the server is listening


server_address = ('192.168.1.123', 20000)
print(server_address)
sock.connect(server_address)


while True:
indicadorplc=(0,1)
    while indicadorplc==(0,1):
        indicadorplc=arm.get_cgpio_digital(0)
        print('plc')
    

   ######Leer QR
    
    
    
    params = {'speed': 100, 'acc': 2000, 'angle_speed': 25, 'angle_acc': 500, 'events': {}, 'variables': variables, 'callback_in_thread': True, 'quit': False}
    arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)
    arm.set_cgpio_digital(0, 0, delay_sec=0)

    
    arm.set_servo_angle(angle=[34.6, 61.0, -103.1, 41.0, 123.5, -63.1], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)
    time.sleep(1)
    print('esperando dato de QR...')
    arm.set_cgpio_digital(3, 1, delay_sec=0)
    time.sleep(0.5)
    arm.set_cgpio_digital(3, 0, delay_sec=0)
    data = sock.recv(1024)
    print('recibido datos de QR...')
    print(data)
    data= data.decode("UTF-8")
    daa = data.split(';')
    Qrpz= str (daa[4])
    print(daa)
    if daa[4]== '':
        print('Qr no encontrado, cambiando a posicion 2')
        arm.set_servo_angle(angle=[2.8, 10.9, -35.8, 35.2, 97.0, -73.7], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)
        time.sleep(1)
        print('esperando dato de QR...')
        arm.set_cgpio_digital(3, 1, delay_sec=0)
        time.sleep(0.5)
        arm.set_cgpio_digital(3, 0, delay_sec=0)
        data = sock.recv(1024)
        print('recibido datos de QR...')
        data= data.decode("UTF-8")
        daa1 = data.split(';')
        Qrpz= str (daa1[4])
        if daa1[4]=='':
            print('No se detecto el Qr')
            arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)
            break
            
        elif daa1[4]!= 'MRD-1000':
            print('La pieza no pertenece a esta linea')
            arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)
            print(daa1[4])
            break
        else:
            print(' La pieza si es de la linea, puede seguir', daa1[4])
    elif daa[4]!= 'MRD-1000':
            print('La pieza no pertenece a esta linea')
            arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)
            print(daa[4])
            break

    else:
        print(' La pieza si es de la linea, puede seguir', daa[4])
            

    time.sleep(3)
   ## Ver que color es
   
   
    params = {'speed': 100, 'acc': 2000, 'angle_speed': 20, 'angle_acc': 500, 'events': {}, 'variables': variables, 'callback_in_thread': True, 'quit': False}
    arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)
    time.sleep(1.5)
    print('Buscando Color...')
    arm.set_cgpio_digital(3, 1, delay_sec=0)
    time.sleep(0.5)
    arm.set_cgpio_digital(3, 0, delay_sec=0)
    data = sock.recv(1024)
    print('recibido...')
    data = data.decode("UTF-8")
    dca = data.split(';')
    colorpz= float (dca[3])
    print(dca[3])
    if colorpz >=75 and colorpz <=80:
        color='Motor rojo'
    elif colorpz >=81 and colorpz <=85:
        color= 'Motor blanco'
    elif colorpz >= 62.7 and colorpz <=72:
        color='Motor azul'
    elif colorpz >=42.55 and colorpz <= 44:
        color='Reductor rojo'
    elif colorpz >=53.5 and colorpz <=55.3:
        color= 'Reductor blanco'
    elif colorpz >= 40 and colorpz <=42.5:
        color= 'Reductor azul'

    
    else:
        print(' No se detecto el color de la pieza')
        break
    print('La pieza es ',color, dca[3])
        

    params = {'speed': 250, 'acc': 2000, 'angle_speed': 50, 'angle_acc': 500, 'events': {}, 'variables': variables, 'callback_in_thread': True, 'quit': False}
    ## MOver Robot
    arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)
    params = {'speed': 270, 'acc': 2000, 'angle_speed': 50, 'angle_acc': 500, 'events': {}, 'variables': variables, 'callback_in_thread': True, 'quit': False}
    print('esperando coordenadas...')
    time.sleep(2)
    arm.set_cgpio_digital(3, 1, delay_sec=0)
    time.sleep(0.5)
    arm.set_cgpio_digital(3, 0, delay_sec=0)
    
    data = sock.recv(1024)
    print('Coordenadas recibidas...')
    print(data)



  
    arm.set_position(z=250,wait=True)

## El dato en X
    data = data.decode("UTF-8")
    dta = data.split(';')
    x1= float (dta[2])
    xmov= -137 + (x1*0.1375)
 
    print ('Valor de x' , xmov)

### El dato en Y
    y1= float (dta[1])
    ymov= 349+(y1*0.134)
    
    print ('Valor de y' , ymov)


    angulo = float(dta[0])
    if angulo < 0:
        angulo=(angulo*-1)
    elif angulo >45:
            angulo=90- angulo
    else:
        angulo= (angulo*-1)+183
        
    print('El angulo es' , angulo)

   
        
        
    if xmov == -137:
        print('No se detecto un patron para acomodar el robot, checa el pinpoint')
        arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)
        break
    else:
        arm.set_position(yaw=angulo)
        arm.set_position(y=ymov,wait=True)
        arm.set_position(x=xmov,wait=True)
        
        arm.set_position(z=185,wait=True)
        arm.set_cgpio_digital(0, 1, delay_sec=0)
    #if angulo != 0:
        #break
    
    
    
    time.sleep(2)
## Ver qr de las bases
    arm.set_servo_angle(angle=[5.2, -28.3, -84.6, 1.6, 112.8, 92.7], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)
    print('esperando datos de las bases QR...')
    arm.set_cgpio_digital(3, 1, delay_sec=0)
    time.sleep(0.5)
    arm.set_cgpio_digital(3, 0, delay_sec=0)
    data = sock.recv(1024)
    print('recibido datos de las bases QR...')
    data = data.decode("UTF-8")
    dba = data.split(',')
    Qrbase= (dba)
    print (Qrbase)    
    str_match =  [s for s in Qrbase if "Motor Rojo" in s]
    str_match1 = [s for s in Qrbase if "Motor Azul" in s]
    str_match2 = [s for s in Qrbase if "Motor Blanco" in s]
    str_match3 = [s for s in Qrbase if "Reductor Azul" in s]
    str_match4 = [s for s in Qrbase if "Reductor Blanco" in s]
    str_match5 = [s for s in Qrbase if "Reductor Rojo" in s]


########### Ensamble Rojo con el reductor ya en la base


    if   str_match ==  [] and color == 'Motor rojo':
        print('Ya existe un motor azul ahi, moviendo a zona neutral')
        arm.set_servo_angle(angle=[52.4, -1.9, -19.6, -2.5, 22.6, -34.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0) ##zona neutra
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                           # trigger abrir gripper
        arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)#revisar conveyor
        break

    if   str_match1 ==  [] and color == 'Motor azul':
        print('Ya existe un motor azul ahi, moviendo a zona neutral')
        arm.set_servo_angle(angle=[52.4, -1.9, -19.6, -2.5, 22.6, -34.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0) ##zona neutra
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                           # trigger abrir gripper
        arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)#revisar conveyor
        break
    if   str_match2 ==  [] and color == 'Motor blanco':
        print('Ya existe un motor azul ahi, moviendo a zona neutral')
        arm.set_servo_angle(angle=[52.4, -1.9, -19.6, -2.5, 22.6, -34.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0) ##zona neutra
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                           # trigger abrir gripper
        arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)#revisar conveyor
        break
    if   str_match3 ==  [] and color == 'Reductor azul':
        print('Ya existe un motor azul ahi, moviendo a zona neutral')
        arm.set_servo_angle(angle=[52.4, -1.9, -19.6, -2.5, 22.6, -34.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0) ##zona neutra
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                           # trigger abrir gripper
        arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)#revisar conveyor
        break
    if   str_match4 ==  [] and color == 'Reductor blanco':
        print('Ya existe un motor azul ahi, moviendo a zona neutral')
        arm.set_servo_angle(angle=[52.4, -1.9, -19.6, -2.5, 22.6, -34.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0) ##zona neutra
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                           # trigger abrir gripper
        arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)#revisar conveyor
        break
    if   str_match5 ==  [] and color == 'Reductor rojo':
        print('Ya existe un motor azul ahi, moviendo a zona neutral')
        arm.set_servo_angle(angle=[52.4, -1.9, -19.6, -2.5, 22.6, -34.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0) ##zona neutra
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                           # trigger abrir gripper
        arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)#revisar conveyor
        break
    
    if str_match5 ==[] and color =='Motor rojo':
        if str_match5 == [s for s in Qrbase if "Reductor Rojo" in s]:
            print('Haciendo ensamle rojo')
            print('Dejando motor rojo')
            arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                               #Posicion seguro
            arm.set_servo_angle(angle=[-39.8, -26.8, -20.2, -0.6, 46.9, -87.0], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  #arriba motor rojo
            arm.set_servo_angle(angle=[-40.1, -7.8, -16.2, -1.1, 24.0, -86.7], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)     #dejar motor rojo
            arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                                                                                                                #trigger de abrir gripper 
            arm.set_servo_angle(angle=[-39.8, -26.8, -20.2, -0.6, 46.9, -87.0], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  #arriba motor rojo
            arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                               # Posicion Seguro
            print('Agarrando reductor rojo')
            arm.set_servo_angle(angle=[2.1, -49.9, -6.7, 0.4, 57.6, -0.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)           #arriba reductor rojo
            arm.set_servo_angle(angle=[1.9, -21.9, -2.3, 0.8, 24.3, -1.4], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)           #bajar a reductor rojo
            arm.set_cgpio_digital(0, 1, delay_sec=0)                                                                                                                                                                                               #trigger de cerrar gripper
            arm.set_servo_angle(angle=[2.1, -49.9, -6.7, 0.4, 57.6, -0.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)           #arriba reductor rojo
            arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                             # Posicion Seguro
            arm.set_servo_angle(angle=[-40.3, -26.2, -20.6, -0.6, 46.7, -87.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  #arriba ensamble rojo
            params={'speed': 50, 'acc': 1000, 'angle_speed': 20, 'angle_acc': 500, 'events': {}, 'variables': variables, 'callback_in_thread': True, 'quit': False}                   # ambio de parametros
            arm.set_servo_angle(angle=[-40.8, -19.6, -18.5, -0.7, 37.9, -87.9], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  # abajo ensamble rojo
            arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                                                                                                               #trigger
            arm.set_servo_angle(angle=[-40.3, -26.2, -20.6, -0.6, 46.7, -87.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  #arriba motor rojo
            arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                             #Posicion seguro
            print('Ensamble terminado')

            
   ############# Dejar motor rojo
   
   
    elif str_match== [s for s in Qrbase if "Motor Rojo" in s]  and color=='Motor rojo':
        print('dejando motor rojo')
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                         #Posicion seguro
        arm.set_servo_angle(angle=[-39.8, -26.8, -20.2, -0.6, 46.9, -87.0], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  #arriba motor rojo
        arm.set_servo_angle(angle=[-40.1, -7.8, -16.2, -1.1, 24.0, -86.7], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0) #dejar motor rojo
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                             #trigger
        arm.set_servo_angle(angle=[-39.8, -26.8, -20.2, -0.6, 46.9, -87.0], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  #arriba motor rojo
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                         # Posicion Seguro
        print('Pieza en su lugar')
    


########### Ensamble Blanco con el reductor ya en la base


    if str_match4 ==[] and color =='Motor blanco':
        if str_match2 == [s for s in Qrbase if "Motor Blanco" in s]:
            
            print('Haciendo Ensamble Blanco')
            print('Dejando Motor Blanco')
            
            arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                               #Posicion seguro
            arm.set_servo_angle(angle=[-29.5, -14.2, -42.0, -1.6, 56.4, -79.2], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)   # arriba motor blanco
            arm.set_position(*[334.8, -190.9, 174.6, -179.9, 1.3, 50.5], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                      # dejar motor blanco
            arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                                                                                                                #trigger abrir gripper 
            arm.set_servo_angle(angle=[-29.5, -14.2, -42.0, -1.6, 56.4, -79.2], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)   # arriba motor blanco
            arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                               #Posicion seguro
            print('Agarrando reductor Blanco')
            arm.set_servo_angle(angle=[1.6, -17.6, -29.3, 0.1, 48.3, 1.4], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)          # Arriba reductor blanco
            arm.set_servo_angle(angle=[1.8, -2.3, -23.0, 0.2, 26.6, 1.4], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)            #bajar a  reductor blanco
            arm.set_cgpio_digital(0, 1, delay_sec=0)                                                                                                                                                                                               #trigger de cerrar gripper 
            arm.set_servo_angle(angle=[1.6, -20.6, -31.6, 0.1, 53.6, 1.4], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)          # Arriba reductor blanco
            arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                              #Posicion seguro
            arm.set_servo_angle(angle=[-29.6, -14.4, -44.2, -1.6, 58.6, -86.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  # arriba motor blanco
            arm.set_servo_angle(angle=[-29.8, -2.4, -35.7, -2.2, 38.1, -85.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)      # abajo ensamble blanco
            arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                                                                                                                # trigger de abrir gripper
            arm.set_servo_angle(angle=[-29.6, -14.4, -44.2, -1.6, 58.6, -86.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  # arriba motor blanco
            arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                               #Posicion seguro
            print('Ensamble finalizado')
        
######Dejar Motor blanco


    elif str_match2== [s for s in Qrbase if "Motor Blanco" in s] and color=='Motor blanco':
        print('dejar motor blanco')
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                        #Posicion seguro
        arm.set_servo_angle(angle=[-29.5, -14.2, -42.0, -1.6, 56.4, -79.2], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0) # arriba motor blanco
        arm.set_position(*[334.8, -190.9, 174.6, -179.9, 1.3, 50.5], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                   # dejar motor blanco
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                            #trigger
        arm.set_servo_angle(angle=[-29.5, -14.2, -42.0, -1.6, 56.4, -79.2], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0) # arriba motor blanco
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                        #Posicion seguro
        print('Pieza en su lugar')



############### zona neutral azul


    if str_match3 ==[] and  str_match1 == []:
            arm.set_servo_angle(angle=[52.4, -1.9, -19.6, -2.5, 22.6, -34.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0) ##zona neutra
            arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                           # trigger abrir gripper
            arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)#revisar conveyor
            break
    if str_match2 == [] and str_match4 == []:
            arm.set_servo_angle(angle=[52.4, -1.9, -19.6, -2.5, 22.6, -34.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0) ##zona neutra
            arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                           # trigger abrir gripper
            arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)#revisar conveyor
            break
    if str_match == [] and str_match5== []:
            arm.set_servo_angle(angle=[52.4, -1.9, -19.6, -2.5, 22.6, -34.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0) ##zona neutra
            arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                           # trigger abrir gripper
            arm.set_servo_angle(angle=[102.0, -22.8, -43.5, 0.2, 67.7, 12.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)#revisar conveyor
            break
        

############# Ensamble Azul con el reductor ya en la base


    if str_match3 ==[] and color =='Motor azul':
        if  str_match1 == [s for s in Qrbase if "Motor Azul" in s]:
            print('Haciendo el Ensamble azul')
            arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                               #Posicion seguro
            arm.set_servo_angle(angle=[-22.4, -29.7, -25.5, -0.6, 56.5, -68.6], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)   # arriba motor azul
            arm.set_servo_angle(angle=[-22.2, -6.5, -17.8, -1.2, 25.6, -71.9], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)      #dejar motor azul
            arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                                                                                                                 #trigger abrir gripper
            arm.set_servo_angle(angle=[-22.2, -6.5, -17.8, -1.2, 25.6, -71.9], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)      #dejar motor azul
            arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                               #Posicion seguro
            print('Agarrando el reductor')
            arm.set_servo_angle(angle=[17.4, -29.0, -20.8, 0.6, 51.0, 16.9], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)      #Arriba reductor azul
            arm.set_servo_angle(angle=[17.6, -6.9, -15.4, 1.2, 23.6, 16.4], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)         #dejar reductor azul
            arm.set_cgpio_digital(0, 1, delay_sec=0)                                                                                                                                                                                                 #trigger cerrar gripper
            arm.set_servo_angle(angle=[17.4, -29.0, -20.8, 0.6, 51.0, 16.9], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)       #Arriba reductor azul
            arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                                #Posicion seguro
            arm.set_servo_angle(angle=[-22.4, -29.7, -25.5, -0.6, 56.5, -68.6], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)     # arriba ensamble azul
            params={'speed': 50, 'acc': 1000, 'angle_speed': 20, 'angle_acc': 500, 'events': {}, 'variables': variables, 'callback_in_thread': True, 'quit': False}                      # ambio de parametros
            arm.set_servo_angle(angle=[-22.6, -18.5, -20.2, -0.8, 40.0, -68.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)      # bajar ensamble
            arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                                                                                                                  # trigger
            arm.set_servo_angle(angle=[-22.4, -29.7, -25.5, -0.6, 56.5, -68.6], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)      # arriba ensamble azul
            arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                                  #Posicion seguro
            print('ensamble finalizado')      
    ######Dejar Motor Azul
    elif color=='Motor azul' and str_match1== [s for s in Qrbase if "Motor Azul" in s] :
        print('dejar motor azul')
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                        #Posicion seguro
        arm.set_servo_angle(angle=[-22.4, -29.7, -25.5, -0.6, 56.5, -68.6], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0) # arriba motor azul
        arm.set_servo_angle(angle=[-22.2, -6.5, -17.8, -1.2, 25.6, -71.9], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  #dejar motor azul
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                            #trigger
        arm.set_servo_angle(angle=[-22.2, -6.5, -17.8, -1.2, 25.6, -71.9], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  #dejar motor azul
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                        #Posicion seguro
        print('Pieza en su lugar')
                       
        

########### Ensamble Blanco con motor ya en la base


    if str_match2==[] and color =='Reductor blanco':
        print('Haciendo el Ensamble blanco')
        print('Dejando reductor blanco')
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                                #Posicion seguro
        arm.set_servo_angle(angle=[-29.5, -14.2, -42.0, -1.6, 56.4, -79.2], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)     # arriba motor blanco
        arm.set_servo_angle(angle=[-29.8, -2.4, -35.7, -2.2, 38.1, -85.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)      # abajo ensamble blanco
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                                                                                                                 # trigger abrir gripper
        arm.set_servo_angle(angle=[-29.5, -14.2, -42.0, -1.6, 56.4, -79.2], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)    # arriba motor blanco
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                                #Posicion seguro
        print('Ensamble finalizado')
    ########## Dejar reductor blanco
    elif str_match4== [s for s in Qrbase if "Reductor Blanco" in s] and color=='Reductor blanco':
        print('Dejando reductor blanco')
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                                  #Posicion seguro
        arm.set_servo_angle(angle=[1.6, -17.6, -29.3, 0.1, 48.3, 1.4], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)              # Arriba reductor blanco
        arm.set_servo_angle(angle=[1.8, -2.3, -23.0, 0.2, 26.6, 1.4], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)                #dejar reductor blanco
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                                                                                                                   #trigger abrir gripper
        arm.set_servo_angle(angle=[1.6, -20.6, -31.6, 0.1, 53.6, 1.4], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)              # Arriba reductor blanco
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                                   #Posicion seguro
        print('Pieza en su lugar')

             

        


########### Ensamble Azul con motor ya en la base


    if str_match1==[]  and  color =='Reductor azul':
        print('Haciendo el Ensamble azul')
        print('Dejando reductor azul')
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                                    #Posicion seguro
        arm.set_servo_angle(angle=[-22.4, -29.7, -25.5, -0.6, 56.5, -68.6], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)        # arriba ensamble azul
        params={'speed': 50, 'acc': 1000, 'angle_speed': 20, 'angle_acc': 500, 'events': {}, 'variables': variables, 'callback_in_thread': True, 'quit': False}                         # ambio de parametros
        arm.set_servo_angle(angle=[-22.6, -18.5, -20.2, -0.8, 40.0, -68.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)        # bajar ensamble
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                                                                                                                     # trigger abrir gripper
        arm.set_servo_angle(angle=[-22.4, -29.7, -25.5, -0.6, 56.5, -68.6], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)        # arriba ensamble azul
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                                    #Posicion seguro
        print('Ensamble finalizado')
     #######Dejar Reductor Azul
    elif str_match3 == [s for s in Qrbase if "Reductor Azul" in s]and color== 'Reductor azul':
    
        print('Dejar reductor azul')
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                   #Posicion seguro
        arm.set_servo_angle(angle=[17.4, -29.0, -20.8, 0.6, 51.0, 16.9], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)#Arriba reductor azul
        arm.set_servo_angle(angle=[17.4, -9.5, -15.3, 1.1, 26.1, 16.4], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)#dejar reductor azul
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                       #trigger
        arm.set_servo_angle(angle=[17.4, -29.0, -20.8, 0.6, 51.0, 16.9], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)#Arriba reductor azul
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                   #Posicion seguro
        print('Pieza en su lugar')
        
        
        


########### Ensamble Rojo con motor ya en la base


    if str_match== [] and  color=='Reductor rojo':
        os.system ("cls")
        print('Haciendo ensamble rojo')
        print('Dejando reductor rojo')
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                                    #Posicion seguro
        arm.set_servo_angle(angle=[-40.3, -26.2, -20.6, -0.6, 46.7, -87.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)        #arriba ensamble rojo
        params={'speed': 50, 'acc': 1000, 'angle_speed': 20, 'angle_acc': 500, 'events': {}, 'variables': variables, 'callback_in_thread': True, 'quit': False}                         # ambio de parametros
        arm.set_servo_angle(angle=[-40.8, -19.6, -18.5, -0.7, 37.9, -87.9], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)        # abajo ensamble rojo
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                                                                                                                     #trigger abrir gripper
        arm.set_servo_angle(angle=[-40.3, -26.2, -20.6, -0.6, 46.7, -87.5], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)        #arriba motor rojo
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                                                    #Posicion seguro
        print('Ensamble finalizado')
######Dejar Reductor Rojo


    elif str_match5==  [s for s in Qrbase if "Reductor Rojo" in s] and color=='Reductor rojo':
        print('dejar reductor rojo')
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                    #Posicion seguro
        arm.set_servo_angle(angle=[2.1, -49.9, -6.7, 0.4, 57.6, -0.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  #arriba reductor rojo
        arm.set_servo_angle(angle=[1.9, -24.5, -2.4, 0.7, 27.0, -1.3], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  #dejar reductor rojo
        arm.set_cgpio_digital(0, 0, delay_sec=0)                                                                                                        #trigger
        arm.set_servo_angle(angle=[2.1, -49.9, -6.7, 0.4, 57.6, -0.8], speed=params['angle_speed'], mvacc=params['angle_acc'], wait=True, radius=-1.0)  #arriba reductor rojo        
        arm.set_position(*[334.8, 0.1, 336.9, 180.0, 1.4, 0.1], speed=params['speed'], mvacc=params['acc'], radius=-1.0, wait=False)                    #Posicion seguro
        print('Pieza en su lugar')

            
        
    
    if color=='':
        break
    
        
        

    

arm.disconnect()
sock.close


