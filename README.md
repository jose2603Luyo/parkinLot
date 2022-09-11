# parkinLot

This excercise is shown to deal with some design problems, most of the times used during design interviews. 

A)Approach:
As I have no more info than the the described below, I will just provide the design of the solution
(relationship between identified clasess and the design of these) and the code in Java.

B) State:
"We have a parking lot with several levels and different entrances. We want to implement a utility that would give new cars coming in parking spots that are available.
Let’s say that we have 4 entrances, when 4 cars come in, each entrance will show the incoming car the parking spot that car needs to go to.
Please handle additional requirements if the entrance doesn’t show the spot, instead after a car takes a spot, that spot is taken out from the available spots list.
When a car leaves a spot, the spot is put back in the pool."

C)Solution

First, Let's identify the classes involved:

a)ParkinLot=>Where the levels are placed
  Relationship: 1..*(1 ParkinLot can have N levels)
b)Level=> Where the parkingSlots are placed
  Relationship: 1..*(1 Level can have N parkinSlots)
c)ParkinSlots=> It's the spot where a car will be placed.
  RelationShip:1..1(1 parkinfSlot can allocate 1 car)
d)car=>It's the current car we are allocatin

e)Considerations:
  
  
  1.-We are not considering sizes for a car, they can be either small, medium or big.
  2.-We are considerin 10 slots per row.(Just to make it easy)
  3.-We'll provide 3 methods(park(), unPark() and findAvailableSpace()).
  
 
Code in Java
 
Car.java

    public class Car {
    public String licenseNumber;

    public Car(String licenseNumber){
        this.licenseNumber = licenseNumber;
    }
    
    public String getLicenseNumber(){
     return this.licenseNumber;
    }
    }

ParkinSlot.java

    public class ParkingSlot {
      public int row;
      public int col;
      public int level;
      public Car carOccupyingSlot;

      public ParkingSlot(int row, int col, int level){
        this.row = row;
        this.col = col;
        this.level = level;
    }

    public void park(Car car){
        this.carOccupyingSlot = car;
    }
    
    public int getRow(){
    return this.row;
    
    }
    
    public int getCol(){
    return this.col;
    
    }
    }
 
 Level.java
 
    import java.util.LinkedList;
    import java.util.List;
    
    public class Level {
    
    public int levelNumber;
    public int rows;
    public List<ParkingSlot> takenSlots;
    public int SLOTS_PER_ROW = 2;//this value can be configured

    public Level(int rows, int levelNumber){
        this.levelNumber = levelNumber;
        this.rows = rows;
        this.takenSlots = new LinkedList<>();//used to get a constant time complexity

    }
    public int getLevelNumber(){
    
    return this.levelNumber;
    
    }
    
     

    public static ParkingSlot findAvailableSpot(){
        int totalNumberSlots = rows * SLOTS_PER_ROW;
        if(takenSlots.size()>=totalNumberSlots){
            // parking full
            System.out.println("The Parking is Full!");
            return null;
        }else if(takenSlots.isEmpty()){
            return new ParkingSlot(0,0,levelNumber);
        }else{
            ParkingSlot lastSpaceOccupied = takenSlots.get(takenSlots.size()-1);
            if(lastSpaceOccupied.col<SLOTS_PER_ROW){
                return new ParkingSlot(lastSpaceOccupied.row, lastSpaceOccupied.col+1,levelNumber);
            }else{
                return new ParkingSlot(lastSpaceOccupied.row+1,0,levelNumber);
            }
        }
    }

    public boolean park(Car car){
        ParkingSlot freeSpace = findAvailableSpot();
        if(freeSpace ==null){
            return false;
        }else{
            freeSpace.park(car);
     lyy       takenSlots.add(freeSpace);
            return true;
        }
    }
    
    public boolean unPark(Car car){
    ParkingSlot removal =null;
     for(ParkingSlot slot: takenSlots){
      if(slot.carOccupyingSlot.getLicenseNumber().equalsIgnoreCase(car.getLicenseNumber())){
       removal=slot;
      }
     }
     takenSlots.remove(removal); 
     
     return true;
    }

ParkingLot.java

      import java.util.List;

      public class ParkingLot {

      public List<Level> levels;

       public ParkingLot(List<Level> levels){
         this.levels = levels;
     }
     public boolean park(Car car){
      ParkingSlot available=Level.findAvailableSpot();  
        for(Level level:levels){
            if(level.park(car)){  
             System.out.println("The car:"+ car.getLicenseNumber() + "should go to the parkin Slot: (" + available.getCol()+ " , " + available.getRow() + " )" + "in the level:"+ level.getLevelNumber());
                return true;
                
            }else{
                continue;
            }
        }
        
        return false;
       }
       
       public boolean unpark(Car car){
        for(Level level:levels){
            if(level.unpark(car)){
            System.out.println("The car:"+ car.getLicenseNumber() + "has been unparked" + "in the level:"+ level.getLevelNumber());
                return true;
            }else{
                continue;
            }
        }
        return false;
       }
       
       
       
       }
  
  Test.Java
  

    import org.junit.Test;
    import parkinglot.solution.Car;
    import parkinglot.solution.Level;
    import parkinglot.solution.ParkingLot;

    import java.util.List;

    import static org.junit.Assert.assertFalse;
    import static org.junit.Assert.assertTrue;

    public class ParkingLotTest {

        @Test
        public void test1(){
        
        //We alloacate the parkingLot with 2 levels, each level with 2 rows
              List<Level> levels = List.of(new Level(2,0), new Level(2,1));
            
        //We assign these levels to the ParkinLot
              ParkingLot parkingLot = new ParkingLot(levels);
              
        //We'll get the respective message indicating where the the cars should go
              assertTrue(parkingLot.park(new parkinglot.solution.Car("LABC")));
              assertTrue(parkingLot.park(new parkinglot.solution.Car("LAAB")));
              assertTrue(parkingLot.park(new parkinglot.solution.Car("LAAA")));
              assertTrue(parkingLot.park(new parkinglot.solution.Car("LAAC")));

            

              assertTrue(parkingLot.park(new parkinglot.solution.Car("TEST")));
              assertTrue(parkingLot.park(new parkinglot.solution.Car("TEST")));
              assertTrue(parkingLot.park(new parkinglot.solution.Car("LUY1")));
              assertTrue(parkingLot.park(new parkinglot.solution.Car("LUY2")));

              
        //We'll get the message indicating the parking is full as we are surpassing the limit.
              assertFalse(parkingLot.park(new Car("L126")));

        //We'll get the message indicating the car has been removed
            assertTrue(parkingLot.unpark(new parkinglot.solution.Car("LABC")));
          }
      }


 
