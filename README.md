Viešbučio valdymo sistema

Įvadas

Ši programa yra viešbučio valdymo sistema, parašyta naudojant Python. Ji naudoja keturis objektinio programavimo principus (enkapsuliacija, paveldėjimas, polimorfizmas, abstrakcija) ir du dizaino šablonus (Singleton ir Factory Method). Ši sistema leidžia pridėti kambarius, valdyti jų užimtumą, pateikti svečių prašymus ir teikti viešbučio paslaugas. Programa skirta būti paleista ir naudojama tiek per konsolę, tiek per Google Colab.

Kaip paleisti programą

Atsisiųskite Python ir įdiekite jį, jei dar neturite.
Klonuokite GitHub repozitoriją į savo vietinį kompiuterį.
Paleiskite programą per konsolę ar Python interpretuotuvą.
Naudokite programą pagal pateiktus pavyzdžius.

Kaip naudoti programą

Pridėti kambarius: Naudokite add_room metodą pridėti naujus kambarius.
Peržiūrėti kambarių sąrašą: Naudokite list_rooms metodą peržiūrėti visų kambarių sąrašą.
Užregistruoti svečią į kambarį: Naudokite check_in_room metodą užregistruoti svečią į kambarį.
Išregistruoti svečią iš kambario: Naudokite check_out_room metodą išregistruoti svečią iš kambario.

Kodo analizė

Naudojami principai

Enkapsuliacija (Encapsulation):

Enkapsuliacija leidžia paslėpti objekto vidinę būseną ir užtikrinti, kad prie jos galima būtų prieiti tik per viešus metodus.
Room klasėje kintamieji __room_number, __room_type, __is_occupied yra privatūs ir pasiekiami tik per viešus metodus.
python
Копировать код
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
python
Копировать код
class Suite(Room):
    def __init__(self, room_number):
        super().__init__(room_number, 'Suite')

    def provide_extra_services(self):
        return "Extra services provided"
Polimorfizmas (Polymorphism):

Polimorfizmas leidžia objektams būti naudojamiems per jų bendrą sąsają, nepaisant jų specifinės klasės. Tai leidžia rašyti lankstesnį ir išplečiamą kodą.
Guest ir VIPGuest klasės turi metodą request_service, kuris veikia skirtingai priklausomai nuo klasės.
python
Копировать код
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
python
Копировать код
from abc import ABC, abstractmethod

class HotelService(ABC):
    @abstractmethod
    def provide_service(self):
        pass

class RoomService(HotelService):
    def provide_service(self):
        return "Room service provided"
Naudojami šablonai
Singleton šablonas:

Singleton šablonas užtikrina, kad klasė turės tik vieną egzempliorių ir suteikia globalią prieigos tašką.
HotelManager klasė naudojasi šiuo šablonu, kad užtikrintų vienintelį viešbučio valdytojo egzempliorių.
python
Копировать код
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
Factory Method šablonas:

Factory Method šablonas suteikia būdą kurti objektus neatskleidžiant tikslaus kūrimo logikos, naudojamas kuriant įvairių tipų kambarius.
RoomFactory klasė naudojasi šiuo šablonu, kad sukurtų kambarius pagal jų tipą.
python
Копировать код
class RoomFactory:
    @staticmethod
    def create_room(room_type, room_number):
        if room_type == "Suite":
            return Suite(room_number)
        else:
            return Room(room_number, room_type)
Kodo naudojimas
python
Копировать код
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
python
Копировать код
# Usage example
hotel_data = HotelData()
hotel_data.add_room("Standard", 101)
hotel_data.add_room("Suite", 102)
hotel_data.add_room("Standard", 103)
hotel_data.add_room("Standard", 104)
hotel_data.add_room("Suite", 201)
hotel_data.add_room("Standard", 202)
hotel_data.add_room("Suite", 203)
hotel_data.add_room("Standard", 204)

print("List of all rooms after adding:")
hotel_data.list_rooms()

print("\nChecking into rooms 101 and 201:")
hotel_data.check_in_room(101)
hotel_data.check_in_room(201)

print("\nList of all rooms after check-in:")
hotel_data.list_rooms()

print("\nChecking out of room 101:")
hotel_data.check_out_room(101)

print("\nFinal list of all rooms:")
hotel_data.list_rooms()
Testavimas
Unit testai naudojant unittest:

Pagrindinių funkcijų testavimas užtikrina, kad kambarių registracija ir išregistracija veikia teisingai, o HotelManager klasė išlaiko vienintelio egzemplioriaus savybę.
