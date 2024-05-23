#Viešbučio valdymo sistema

#Įvadas

Ši programa yra viešbučio valdymo sistema, parašyta naudojant Python. Ji naudoja keturis objektinio programavimo principus (enkapsuliacija, paveldėjimas, polimorfizmas, abstrakcija) ir du dizaino šablonus (Singleton ir Factory Method). Ši sistema leidžia pridėti kambarius, valdyti jų užimtumą, pateikti svečių prašymus ir teikti viešbučio paslaugas. Programa skirta būti paleista ir naudojama tiek per konsolę, tiek per Google Colab.


#Kaip naudoti programą

1.Pridėti kambarius: Naudokite add_room metodą pridėti naujus kambarius.
2.Peržiūrėti kambarių sąrašą: Naudokite list_rooms metodą peržiūrėti visų kambarių sąrašą.
3.Užregistruoti svečią į kambarį: Naudokite check_in_room metodą užregistruoti svečią į kambarį.
4.Išregistruoti svečią iš kambario: Naudokite check_out_room metodą išregistruoti svečią iš kambario.

#Kodo analizė

Naudojami principai:

Enkapsuliacija (Encapsulation):

Enkapsuliacija leidžia paslėpti objekto vidinę būseną ir užtikrinti, kad prie jos galima būtų prieiti tik per viešus metodus.
Room klasėje kintamieji __room_number, __room_type, __is_occupied yra privatūs ir pasiekiami tik per viešus metodus.

class Room:
    def __init__(self, room_number, room_type):
        self.__room_number = room_number
        self.__room_type = room_type
        self.__is_occupied = False

    def check_in(self):
        if not self.__is_occupied:
            self.__is_occupied = True
        else:
            raise Exception("Room is already occupied")

    def check_out(self):
        if self.__is_occupied:
            self.__is_occupied = False
        else:
            raise Exception("Room is already vacant")

    def is_occupied(self):
        return self.__is_occupied

    def get_room_number(self):
        return self.__room_number

    def get_room_type(self):
        return self.__room_type
Paveldėjimas (Inheritance):

Paveldėjimas leidžia sukurti naujas klases, kurios paveldi savybes ir metodus iš egzistuojančių klasių, taip leidžiant pakartotinai naudoti kodą ir kurti labiau specializuotas klases.
Suite klasė paveldi Room klasę ir prideda papildomą metodą provide_extra_services.

class Suite(Room):
    def __init__(self, room_number):
        super().__init__(room_number, 'Suite')

    def provide_extra_services(self):
        return "Extra services provided"
Polimorfizmas (Polymorphism):

Polimorfizmas leidžia objektams būti naudojamiems per jų bendrą sąsają, nepaisant jų specifinės klasės. Tai leidžia rašyti lankstesnį ir išplečiamą kodą.
Guest ir VIPGuest klasės turi metodą request_service, kuris veikia skirtingai priklausomai nuo klasės.

class Guest:
    def __init__(self, name):
        self.name = name

    def request_service(self):
        return f"Service requested by {self.name}"

class VIPGuest(Guest):
    def request_service(self):
        return f"VIP service requested by {self.name}"
Abstrakcija (Abstraction):

Abstrakcija leidžia sukurti bendrą sąsają, kurią privalo realizuoti konkrečios klasės. Tai leidžia rašyti kodą, kuris gali dirbti su bet kokia klase, įgyvendinančia šią sąsają, nepaisant specifinių detalių.
HotelService abstrakti klasė turi abstraktų metodą provide_service, kurį privalo realizuoti paveldinčios klasės.

from abc import ABC, abstractmethod

class HotelService(ABC):
    @abstractmethod
    def provide_service(self):
        pass

class RoomService(HotelService):
    def provide_service(self):
        return "Room service provided"
        
#Naudojami šablonai

#Singleton šablonas:

Singleton šablonas užtikrina, kad klasė turės tik vieną egzempliorių ir suteikia globalią prieigos tašką.
HotelManager klasė naudojasi šiuo šablonu, kad užtikrintų vienintelį viešbučio valdytojo egzempliorių.

class HotelManager:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super(HotelManager, cls).__new__(cls)
        return cls._instance

    def __init__(self):
        if not hasattr(self, 'rooms'):
            self.rooms = []

    def add_room(self, room):
        self.rooms.append(room)

    def get_rooms(self):
        return self.rooms
#Factory Method šablonas:

Factory Method šablonas suteikia būdą kurti objektus neatskleidžiant tikslaus kūrimo logikos, naudojamas kuriant įvairių tipų kambarius.
RoomFactory klasė naudojasi šiuo šablonu, kad sukurtų kambarius pagal jų tipą.

class RoomFactory:
    @staticmethod
    def create_room(room_type, room_number):
        if room_type == "Suite":
            return Suite(room_number)
        else:
            return Room(room_number, room_type)
Kodo naudojimas

# Functions for in-memory data handling
class HotelData:
    def __init__(self):
        self.manager = HotelManager()

    def add_room(self, room_type, room_number):
        room = RoomFactory.create_room(room_type, room_number)
        self.manager.add_room(room)

    def list_rooms(self):
        for room in self.manager.get_rooms():
            print(f"Room Number: {room.get_room_number()}, Room Type: {room.get_room_type()}, Is Occupied: {room.is_occupied()}")

    def check_in_room(self, room_number):
        for room in self.manager.get_rooms():
            if room.get_room_number() == room_number:
                room.check_in()
                print(f"Room {room_number} checked in.")
                return
        print(f"Room {room_number} not found.")

    def check_out_room(self, room_number):
        for room in self.manager.get_rooms():
            if room.get_room_number() == room_number:
                room.check_out()
                print(f"Room {room_number} checked out.")
                return
        print(f"Room {room_number} not found.")

#Testavimas

Unit testai naudojant unittest:

Pagrindinių funkcijų testavimas užtikrina, kad kambarių registracija ir išregistracija veikia teisingai, o HotelManager klasė išlaiko vienintelio egzemplioriaus savybę.

#Išvada

Šiame projekte buvo sukurta viešbučio valdymo sistema, panaudojant keturis pagrindinius objektinio programavimo principus ir du svarbius dizaino šablonus. Projekto tikslas buvo parodyti, kaip galima efektyviai valdyti viešbučio kambarius ir svečių registraciją, naudojant struktūruotą ir lengvai plečiamą kodą.

#Bendras įvertinimas

Ši viešbučio valdymo sistema yra sėkmingas objektinio programavimo principų ir dizaino šablonų taikymo pavyzdys. Ji užtikrina struktūruotą, lankstų ir lengvai plečiamą kodą, kuris gali būti pritaikytas įvairioms situacijoms ir poreikiams. Projekto metu buvo išspręsti keli iššūkiai, o galutinė sistema atitinka pagrindinius viešbučio valdymo reikalavimus.
