	import java.io.File;
	import java.io.FileNotFoundException;
	import java.io.FileReader;
	import java.io.Reader;
	import java.nio.file.Path;
	import java.util.Scanner;
	import java.util.stream.Stream;
	import java.util.ArrayList;
	import java.util.LinkedList;
	import java.util.List;
	import java.util.ListIterator;

public class CourseWork1 {
	
	public final String SEP = ",";
	// sets the variable to global variables
	public int YEAR;
	public int MONTH;
	public double MAX_TEMP;
	public double MIN_TEMP;
	public double FROSTY_DAYS;
	public double RAIN_IN_MM;
	public double HOURS_OF_SUN;
	//setting up the weather data and the file names as arrays
	public static ArrayList<Weather> weatherData = new ArrayList<Weather>();
	public static ArrayList<String> fileNames = new ArrayList<>();
	
	//this method loads the menu method and the load weather method as well which loads the data 
	public static void main (String[] args) throws FileNotFoundException
	{
		
		loadWeather(); 
		showMenu();
	}
	
	public CourseWork1(String csvString)
	{
		Scanner input = new Scanner(System.in);
		String[]csvParts = csvString.split(SEP, -1);
		int idx=0;
		YEAR = Integer.valueOf(csvParts[idx++]);
		MONTH = Integer.valueOf(csvParts[idx++]);
		MAX_TEMP = Double.valueOf(csvParts[idx++]);
		MIN_TEMP = Double.valueOf(csvParts[idx++]);
		FROSTY_DAYS = Double.valueOf(csvParts[idx++]);
		RAIN_IN_MM = Double.valueOf(csvParts[idx++]);
		HOURS_OF_SUN = Double.valueOf(csvParts[idx++]);

}
	/* 
	 this is where the loading of the files come into play, we did something easy for us and that was to read from a folder not by multiple files.
	  we stored the files in a array and looped over the file names and printed out the path to the files.
	 
	 */
	public static void loadWeather() throws FileNotFoundException 
	{
		File folder = new File("src/data");
		File[] listOfFiles = folder.listFiles();
		
		for (File f : listOfFiles) 
		{
			fileNames.add(f.getName());
			
			System.out.println(f.getAbsolutePath());
		    FileReader fr = new FileReader(f);
		    Scanner fileScanner = new Scanner(fr);
		    boolean atData = false;
		    // this loops of the scanner and if there data is false then replace it to a empty string with a space
	    	while (fileScanner.hasNextLine())
	    	{		    		
	    		String line = fileScanner.nextLine();
	    		
	    		if (atData)
	    		{
	    			line = line.trim().replaceAll("\\s{2,}", " ");
		    		String[] lineParts = line.split(" ");
		    		//if the array is greater than 4 then store variables into a array
		    		if (lineParts.length > 4)
		    		{
		    			int year = Integer.parseInt(lineParts[0]);
			            
		    			int month = Integer.parseInt(lineParts[1]);
		    			
		    			Double max_temp = cleanDouble(lineParts[2]);
		    			
		    			Double min_temp = cleanDouble(lineParts[3]);
		    			
		    			Double frosty_days = cleanDouble(lineParts[4]);
		    			
		    			Double rain_in_mm = cleanDouble(lineParts[5]);
		    			
		    			// since the array stops working after 6 inputs we use a if it equals to 7 or greater then prints of the last array
		    			Double hours_of_sun = null;
		    			
		    			if (lineParts.length >= 7)
		    				hours_of_sun = cleanDouble(lineParts[6]);
		    			// stores all the data into the class we called weather
			           Weather weather = new Weather(year, month, max_temp, min_temp, frosty_days, rain_in_mm, hours_of_sun, f.getName());
			           weatherData.add(weather);
		    		}
	    		}
	    		//if the line equals to the string then the data is true
	    		if (line.equals("              degC    degC    days      mm   hours"))
	    			atData = true;
	    	}
		}
		
	}
	//this gets rid of any unwanted lines of text or any unnecessary symbols in the text files so it can be read.
	public static Double cleanDouble(String s)
	{
		if (s.equals("---"))
			return null;
		
		String tempString = "";
		 for (int i = 0; i < s.length(); i++) 
		 {
		        if (Character.isDigit(s.charAt(i)) || s.charAt(i) == '.') 
		        {
		           tempString += s.charAt(i);
		        }
		 }
		 
		 s = tempString;
		
		return Double.parseDouble(s);
	}
	// displays the main menu and gives the user to pick from a couple of choices which will either display what they are asking for or get another user input
    public static void showMenu() throws FileNotFoundException {
    	Scanner input = new Scanner(System.in);
    	String choice = "";
        
        do {
               // Shows the main menu for the user which lets them get access to the files.
               System.out.println("\n-- Welcome to the main menu --");
               System.out.println("\n-- What would you like to do? --");
               System.out.println("1 - Select Location");
               System.out.println("2 - Print all data");
               System.out.println("3 - Filter by date");
               System.out.println("Q - Quit");
               System.out.print("Pick : ");
               
               choice = input.next().toUpperCase(); // Sets the menu choice variable based on what the user types in
               
               switch (choice) 
               {
                     case "1":
                     {
                    	 String userAnswer = "";
                    	 
                    	 System.out.println("\n-- View Files --");
                         System.out.println("\n-- Which file would you like to look at? --");
                 	     // this gets the file names stored in the array filenames, and displays the name and replaces the .txt with a empty string  
                         do {
                        	 // Shows the main menu for the user which lets them get access to the files.
                        	 int count = 1;
                 	         for (String s : fileNames)
                 	         {
                 	        	 System.out.println(count + " - " + s);
                 	        	 count++;
                 	          }

                 	         System.out.println("Q - Quit");
                 	         System.out.print("Pick : ");
                 	               
                 	         userAnswer = input.next().toUpperCase(); // Sets the menu choice variable based on what the user types in
                 	         userAnswer = fileNames.get((Integer.valueOf(userAnswer) - 1)).replaceAll(".txt", "");
                 	         filterByLocation(userAnswer);

                 	         //exits the program for the user
                         } while (!userAnswer.equalsIgnoreCase("Q"));
                         input.close();
                         System.out.println("Bye Bye :)");
                         break;
                     }
                     case "2": 
                     {
	                     	
                         
                    	 printAllData();
                    	 break;
                     }
                     case "3": 
                     {
                    	 //filters the date by month and year by asking for user input
                    	 System.out.println("\n-- Filter Date --");
	                        System.out.println("Please enter a given month: ");
	                        int month = input.nextInt();
	                        System.out.println("Please enter a given Year: ");
	                        int year = input.nextInt();
	                        //stores user choice into filterdate method
                    	 filterDate(year, month);
                    	 break;
                     }
                     
                     case "4": 
                     {   
                    	 //ArrayList<Integer> = filterByLocation(userAnswer);
                    	 // filterTemp(min, max,);
                    	 break;
                     }
                    
               }
        } while (!choice.equalsIgnoreCase("Q"));
        
        input.close();
        System.out.println("Bye Bye :)");
    }
    //this gets all the data in each file and displays it all at once
    public static void printAllData()
    {
    	for (int i = 0; i < weatherData.size(); i++)
        {
    		weatherData.get(i).printData();
        }
    }
    /*
     this filters the data, for all the weather data get the year and the month and display the users choice of month and year which the data has it
     */
    public static void filterDate(int year, int month)
    {          
	    for (int i = 0; i < weatherData.size(); i++)
	    {
	 	  if (weatherData.get(i).year == year && weatherData.get(i).month == month)// (weatherData.get(i).month.equals(month))
	 	  {
	 		  weatherData.get(i).printData();
	 	  }
	    }
    }
    //filters by the min and max temp which its been passed to and prints it out
    public static void filterTemp(double min, double max)
    {
    	for (int i = 0; i < weatherData.size(); i++)
        {
    		weatherData.get(i).printData();
        }
    }
    //this is a array method filter by location, puts it in a array loops over the weather data where it equals the location then displays it, after that returns the locations data.
	public static ArrayList<Integer> filterByLocation(String location) 
	{
		ArrayList<Integer> locationData = new ArrayList<Integer>();
		
		for (int i = 0; i < weatherData.size(); i++)
	    {
	 	  if (weatherData.get(i).location.equals(location))
	 	  {
	 		  weatherData.get(i).printData();
	 		  locationData.add(i);
	 	  }
	    }
		
		return locationData;
	}
}

