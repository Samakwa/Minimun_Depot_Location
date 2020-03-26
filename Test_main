import static java.lang.Math.*;
import java.util.Scanner;
import java.util.concurrent.ThreadLocalRandom;
import java.awt.*;
import java.awt.event.*;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.text.DecimalFormat;

import javax.swing.*;

public class Main {
	
	public final static int number_cities = 79; // has to be equal to number of cities in the document
	public static int amount = 3,current_amount=4; // amount defines no. of warehouses in starting demo; current_amount no. of warehouses in simulation
	public static double transportation_cost = 0.14, overhead_costs = 18000, construction_cost = 130000;
	public static double mean = 0.04, SD = 0.3; // starting values for the specifications
	// following definitions are for the graphical representation only
	public final static int constant_GUI_X = 100, constant_GUI_Y = 150;
	public final static int constant_GUI_X_border = 1450, constant_GUI_Y_difference = 110, constant_GUI_X_2ndborder = 2150;
	public final static int button_width = 500;

    public static void main(String[] args) {
    	City city[] = new City[number_cities]; // intiziliates the city objects 
    	// reads the data from the txt and defines the city objects
		try {
			Scanner in = new Scanner(new FileReader("data.txt"));
	    	int i = 0;
	    	String name;
	    	double longitude, latitude;
	    	int population;
	    	while(in.hasNext()) {
	    		name = in.next();
	    		population = (int) Double.parseDouble(in.next());
	    		longitude = Double.parseDouble(in.next());
	    		latitude = Double.parseDouble(in.next());
	    		city[i] = new City(name,population,longitude,latitude);
	    	    i++;
	    	}
	    	in.close();
		} catch (FileNotFoundException e) {e.printStackTrace();}
		// creates the graphical interface, i.e. amongst others the button listeners and drop-down menus
		createGUI(city);
    }
    
    // this function returns the total distance between each city and its nearest warehouse
    public static double total_distance(City city[], Warehouse warehouse[], int amount) {
    	double total_distance = 0;
    	double distance[] = new double[number_cities];
    	double current;
    	
    	for (int i=0;i<number_cities;i++) {
    		distance[i] = -1; // preliminary distance is defined as -1 in order to identify that it still has to be assigned
    		
    		for (int n=0;n<amount;n++) { // this for-loop calculates the distance to each city
    			current = distance(city[i].getX(),city[i].getY(),warehouse[n].getX(),warehouse[n].getY());
    			if (distance[i] == -1 || current < distance[i]) 
    				distance[i] = current; 
    		}
    		total_distance += distance[i];		
    	}
    	return(total_distance/2); // return the total distance in kilometers not in pixel (thus divided by two)
    }
      
    // this function calculates the euclidian distance between two points
    public static double distance(int x1, int y1, int x2, int y2) {
    	int x_diff, y_diff;
		double distance;
    	
    	x_diff = x1 - x2;
    	y_diff = y1 - y2;
    	distance = sqrt(pow(x_diff,2) + pow(y_diff,2));
    	return(distance);
    }
    	
    // this function calculates the total transportation costs for a given set of warehouses
    public static double total_transportation_costs(City city[], Warehouse warehouse[], int amount) {
    	double total_transportation_costs = 0;
    	double transportation_cost_to_city[] = new double[number_cities];
    	double current;
    	
    	for (int i=0;i<number_cities;i++) {
    		transportation_cost_to_city[i] = -1; // -1 specifies that this variable has to be specified
    		
    		for (int n=0;n<amount;n++) {
    			current = (double) (distance(city[i].getX(),city[i].getY(),warehouse[n].getX(),warehouse[n].getY())*transportation_cost*city[i].getDemand());
    			current = current/1000; // division by 1,000, because the transportation costs are specified per 1,000 units
    			if (transportation_cost_to_city[i] == -1 || current < transportation_cost_to_city[i]) 
    				transportation_cost_to_city[i] = current;
    		}
    		total_transportation_costs += transportation_cost_to_city[i];		
    	}	
    	return total_transportation_costs;
    }
    
    // this function calculates the total demand within in Germany, i.e. the sum of all cities
    public static int total_demand(City[] city) {
    	int total_demand = 0;
    	
    	for (int i=0;i<number_cities;i++) {
    		total_demand += city[i].getDemand();
    	}
    	
    	return total_demand;
    }
    
    // this function minimizes the total transportation costs for a given number of warehouses
    public static Warehouse[] minimize(City city[], int amount, int iterations) {
    	Warehouse[] solution = new Warehouse[amount];
    	Warehouse[] warehouse = new Warehouse[amount];
    	double total_transportation_costs = -1;
    	double current_total_transportation_costs, temporary_total_transportation_costs;
    	int currentX, currentY;
    	int[] random = new int[amount*2];
    	// the following for loop will calculate many local minima and choose the lowest
    		for (int i=0;i<iterations;i++) { 
    			for (int n=0;n<amount;n++) { // this loop generates randomly located warehouses
    				random[n] = ThreadLocalRandom.current().nextInt(0, 1100 + 1);
    				random[amount-n] = ThreadLocalRandom.current().nextInt(250, 1500 + 1);
    				warehouse[n] = new Warehouse(random[n],random[amount-n]);
    			}
    			current_total_transportation_costs = total_transportation_costs(city,warehouse,amount);
    			//***** this loop tries to move the warehouses into all four directions as long as it does reduce the total 
    			// transportation costs *****************//
    			do {
    				temporary_total_transportation_costs = total_transportation_costs(city,warehouse,amount);
    				for (int n=0;n<amount;n++) {
    					currentX = warehouse[n].getX();
    					currentY = warehouse[n].getY();
    					
    					warehouse[n].setX(currentX+1);
    					if(total_transportation_costs(city,warehouse,amount) < current_total_transportation_costs) {
    						currentX++;
    						warehouse[n].setX(currentX);
    						current_total_transportation_costs = total_transportation_costs(city,warehouse,amount);}
    					
    					warehouse[n].setX(currentX-1);
    					if(total_transportation_costs(city,warehouse,amount) < current_total_transportation_costs) {
    						currentX--;
    						warehouse[n].setX(currentX);
    						current_total_transportation_costs = total_transportation_costs(city,warehouse,amount);}
    					
    					warehouse[n].setX(currentX);
    					warehouse[n].setY(currentY+1);
    					if(total_transportation_costs(city,warehouse,amount) < current_total_transportation_costs) {
    						currentY++;
    						warehouse[n].setY(currentY);
    						current_total_transportation_costs = total_transportation_costs(city,warehouse,amount);
    					}

    					warehouse[n].setY(currentY-1);
    					if(total_transportation_costs(city,warehouse,amount) < current_total_transportation_costs) {
    						currentY--;
    						warehouse[n].setY(currentY);
    						current_total_transportation_costs = total_transportation_costs(city,warehouse,amount);
    					}
    					
    					warehouse[n].setY(currentY);
    				}
    			} while(current_total_transportation_costs < temporary_total_transportation_costs);
    			// this if condition saves the warehouse combination, IF it is the current one with the lowest total transportation costs
    			if (total_transportation_costs == -1 || total_transportation_costs(city, warehouse,amount) < total_transportation_costs) {
    				total_transportation_costs = total_transportation_costs(city,warehouse,amount);
    				for (int n=0;n<amount;n++) 
    					{solution[n] = new Warehouse(warehouse[n].getX(),warehouse[n].getY());}
    			}	
    		}
		return solution;
    }
    
    // this function finds the number and location of the warehouses with the lowest production costs
    private static Warehouse[] minimize_all (City city[]) {
    	Warehouse[] warehouse = new Warehouse[10];
    	double[] total_cost_per_variant = new double[10];
    	double min = -1;
    	for (int i=0;i<10;i++) { // calculate the total costs for each possible number of warehouses, i.e. from 1 to 10
    		total_cost_per_variant[i] = total_transportation_costs(city,minimize(city,(i+1),3),(i+1))+(i+1)*overhead_costs;
    		if (i>current_amount) { // this addition ensures, that a new warehouse will only be constructed, if it is profitable within a year
    			total_cost_per_variant[i] += ((i-current_amount)*construction_cost)/12;
    		}
    		if (min == -1 || total_cost_per_variant[i] < min)
    			min = total_cost_per_variant[i]; // saves the minimal costs
    	}
    	for (int i=0;i<10;i++) { // this loop retrieves the combination with the minimal costs
    		if (total_cost_per_variant[i]==min) {
    			warehouse = minimize(city,(i+1),7);
    			current_amount = (i+1);
    		}
    	}
    	amount = current_amount;
    	return warehouse;
    }
    
    // this method creates the Graphical User Interface
    private static void createGUI(City city[]) { 	
        JFrame frame = new JFrame("Facility Location Problem");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JLabel emptyLabel = new JLabel("");
        Dimension screenSize = Toolkit.getDefaultToolkit().getScreenSize();
        emptyLabel.setPreferredSize(new Dimension(screenSize.width, screenSize.height));
        frame.getContentPane().add(emptyLabel, BorderLayout.CENTER);
        
        //Display the window.
        frame.pack();
        frame.setVisible(true);
        
        JPanel panel = new JPanel();
        panel.setLayout(null);
        
        JLabel text_total_distance = new JLabel();
        text_total_distance.setText("Total Distance: NA");
        text_total_distance.setFont(new Font("Arial", Font.PLAIN, 40));
        text_total_distance.setBounds(constant_GUI_X_border, constant_GUI_Y_difference*4, 500, 150);
        panel.add(text_total_distance);
        
        JLabel text_amount_warehouse = new JLabel();
        text_amount_warehouse.setText("No. Warehouses: NA");
        text_amount_warehouse.setFont(new Font("Arial", Font.PLAIN, 40));
        text_amount_warehouse.setBounds(constant_GUI_X_2ndborder, 50+constant_GUI_Y_difference*13, 500, 100);
        panel.add(text_amount_warehouse);
        
        JLabel text_total_costs = new JLabel();
        text_total_costs.setText("Total Costs/Period: NA");
        text_total_costs.setFont(new Font("Arial", Font.PLAIN, 40));
        text_total_costs.setBounds(constant_GUI_X_border, 50+constant_GUI_Y_difference*13, 600, 100);
        panel.add(text_total_costs);
        
        JLabel text_total_demand = new JLabel();
        text_total_demand.setText("Total demand: NA");
        text_total_demand.setFont(new Font("Arial", Font.PLAIN, 40));
        text_total_demand.setBounds(constant_GUI_X_2ndborder, 50+constant_GUI_Y_difference*12, 700, 100);
        panel.add(text_total_demand);
        
        JLabel text_transportation_costs = new JLabel();
        text_transportation_costs.setText("Transportation Costs: NA");
        text_transportation_costs.setFont(new Font("Arial", Font.PLAIN, 40));
        text_transportation_costs.setBounds(constant_GUI_X_border, 50+constant_GUI_Y_difference*12, 700, 100);
        panel.add(text_transportation_costs);
        
        JLabel text_choose_amount = new JLabel();
        text_choose_amount.setText("Choose Warehouses:");
        text_choose_amount.setFont(new Font("Arial", Font.PLAIN, 40));
        text_choose_amount.setBounds(constant_GUI_X_border, constant_GUI_Y_difference*1, 500, 100);
        panel.add(text_choose_amount);
        
        String[] possible_amount_warehouses = {"1","2","3","4","5","6","7","8","9","10"};
        //Create the combo box, select item at index 2.
        //Indices start at 0, so 2 specifies 3 warehouses
        JComboBox choose_amount_warehouses = new JComboBox(possible_amount_warehouses);
        choose_amount_warehouses.setSelectedIndex(2);
        choose_amount_warehouses.setFont(new Font("Arial", Font.PLAIN, 40));
        choose_amount_warehouses.setBounds(constant_GUI_X_2ndborder, constant_GUI_Y_difference*1, 400, 100);
        panel.add(choose_amount_warehouses);
        choose_amount_warehouses.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) { 
            	JComboBox cb = (JComboBox)e.getSource();
            	amount = Integer.parseInt((String)cb.getSelectedItem());
            }
        });
        
        JLabel text_choose_transportation_cost = new JLabel();
        text_choose_transportation_cost.setText("Transportation Cost €/1000 Unit km's: ");
        text_choose_transportation_cost.setFont(new Font("Arial", Font.PLAIN, 40));
        text_choose_transportation_cost.setBounds(constant_GUI_X_border, 50+constant_GUI_Y_difference*5, 750, 100);
        panel.add(text_choose_transportation_cost);
        
        String[] possible_transportation_cost = {"0.10","0.11","0.12","0.13","0.14","0.15","0.16","0.17","0.18","0.19"};
        JComboBox choose_transportation_cost = new JComboBox(possible_transportation_cost);
        choose_transportation_cost.setSelectedIndex(4);
        choose_transportation_cost.setFont(new Font("Arial", Font.PLAIN, 40));
        choose_transportation_cost.setBounds(constant_GUI_X_2ndborder, 50+constant_GUI_Y_difference*5, 400, 100);
        panel.add(choose_transportation_cost);
        choose_transportation_cost.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) { 
            	JComboBox cb = (JComboBox)e.getSource();
            	transportation_cost = Double.parseDouble((String)cb.getSelectedItem());
            }
        });
        
        JLabel text_choose_overhead_costs = new JLabel();
        text_choose_overhead_costs.setText("Overhead costs €/Warehouse: ");
        text_choose_overhead_costs.setFont(new Font("Arial", Font.PLAIN, 40));
        text_choose_overhead_costs.setBounds(constant_GUI_X_border, 50+constant_GUI_Y_difference*6, 700, 100);
        panel.add(text_choose_overhead_costs);
        
        String[] possible_overhead_costs = {"16,000","17,000","18,000","19,000","20,000","21,000","22,000",
        		"23,000","24,000","25,000"};
        JComboBox choose_overhead_costs = new JComboBox(possible_overhead_costs);
        choose_overhead_costs.setSelectedIndex(2);
        choose_overhead_costs.setFont(new Font("Arial", Font.PLAIN, 40));
        choose_overhead_costs.setBounds(constant_GUI_X_2ndborder, 50+constant_GUI_Y_difference*6, 400, 100);
        panel.add(choose_overhead_costs);
        choose_overhead_costs.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) { 
            	JComboBox cb = (JComboBox)e.getSource();
            	 String value = (String)cb.getSelectedItem();
            	 value = value.replaceAll(",","");
            	 overhead_costs = Double.parseDouble(value);
            }
        });
        
        JLabel text_choose_building_costs = new JLabel();
        text_choose_building_costs.setText("Construction Cost €/Warehouse: ");
        text_choose_building_costs.setFont(new Font("Arial", Font.PLAIN, 40));
        text_choose_building_costs.setBounds(constant_GUI_X_border, 50+constant_GUI_Y_difference*7, 700, 100);
        panel.add(text_choose_building_costs);
        
        String[] possible_construction_costs = {"80,000","90,000","100,000","110,000","120,000","130,000","140,000",
        		"150,000","160,000","170,000"};
        JComboBox choose_construction_costs = new JComboBox(possible_construction_costs);
        choose_construction_costs.setSelectedIndex(5);
        choose_construction_costs.setFont(new Font("Arial", Font.PLAIN, 40));
        choose_construction_costs.setBounds(constant_GUI_X_2ndborder, 50+constant_GUI_Y_difference*7, 400, 100);
        panel.add(choose_construction_costs);
        choose_construction_costs.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) { 
            	JComboBox cb = (JComboBox)e.getSource();
            	String value = (String)cb.getSelectedItem();
           	 	value = value.replaceAll(",","");
           	 	construction_cost = Double.parseDouble(value);
            }
        });
        
        JLabel text_choose_demand_SD = new JLabel();
        text_choose_demand_SD.setText("Demand Standard Deviation: ");
        text_choose_demand_SD.setFont(new Font("Arial", Font.PLAIN, 40));
        text_choose_demand_SD.setBounds(constant_GUI_X_border, 50+constant_GUI_Y_difference*8, 700, 100);
        panel.add(text_choose_demand_SD);
        
        String[] possible_demand_SD = {"0.1","0.2","0.3","0.4","0.5","0.6","0.7",
        		"0.8","0.9","1.0"};
        JComboBox choose_demand_SD = new JComboBox(possible_demand_SD);
        choose_demand_SD.setSelectedIndex(2);
        choose_demand_SD.setFont(new Font("Arial", Font.PLAIN, 40));
        choose_demand_SD.setBounds(constant_GUI_X_2ndborder, 50+constant_GUI_Y_difference*8, 400, 100);
        panel.add(choose_demand_SD);
        choose_demand_SD.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) { 
            	JComboBox cb = (JComboBox)e.getSource();
            	SD = Double.parseDouble((String)cb.getSelectedItem());
            }
        });
        
        JLabel text_choose_demand_mean = new JLabel();
        text_choose_demand_mean.setText("Demand Mean: ");
        text_choose_demand_mean.setFont(new Font("Arial", Font.PLAIN, 40));
        text_choose_demand_mean.setBounds(constant_GUI_X_border, 50+constant_GUI_Y_difference*9, 700, 100);
        panel.add(text_choose_demand_mean);
        
        String[] possible_demand_mean = {"0.00","0.02","0.04","0.06","0.08","0.10","0.12",
        		"0.14","0.16","0.18"};
        JComboBox choose_demand_mean = new JComboBox(possible_demand_mean);
        choose_demand_mean.setSelectedIndex(2);
        choose_demand_mean.setFont(new Font("Arial", Font.PLAIN, 40));
        choose_demand_mean.setBounds(constant_GUI_X_2ndborder, 50+constant_GUI_Y_difference*9, 400, 100);
        panel.add(choose_demand_mean);
        choose_demand_mean.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) { 
            	JComboBox cb = (JComboBox)e.getSource();
            	mean = Double.parseDouble((String)cb.getSelectedItem());
            }
        });
        
        JLabel line = new JLabel(new ImageIcon("line.png"));
        line.setBounds(constant_GUI_X_border, constant_GUI_Y_difference*5, 1200, 20);
        panel.add(line);
        
        JLabel[] image_warehouse = new JLabel[10];
        for (int i=0;i<10;i++) {
        	image_warehouse[i] = new JLabel(new ImageIcon("warehouse2.png"));
        	panel.add(image_warehouse[i]);
        }
        
        JLabel[] image_city = new JLabel[number_cities];
        for (int i=0;i<number_cities;i++) {
        	if(city[i].getDemand()>130000)
        		image_city[i] = new JLabel(new ImageIcon("city_red.png"));
        	else if(city[i].getDemand()>50000)
        		image_city[i] = new JLabel(new ImageIcon("city_dark_blue.png"));
        	else if(city[i].getDemand()>20000)
        		image_city[i] = new JLabel(new ImageIcon("city_light_blue.png"));
        	else if(city[i].getDemand()>15000)
        		image_city[i] = new JLabel(new ImageIcon("city_gray.png"));
        	else
        		image_city[i] = new JLabel(new ImageIcon("city_white.png"));
        	image_city[i].setBounds((city[i].getX()+constant_GUI_X),(1500-city[i].getY()+constant_GUI_Y),100,100);
        	panel.add(image_city[i]);
        }              
        
        JButton button_random = new JButton("Random Solution");
        button_random.setBounds(constant_GUI_X_border, constant_GUI_Y_difference*2, button_width, 100);
        button_random.setFont(new Font("Arial", Font.PLAIN, 40));
        button_random.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) { 
            	Warehouse warehouse[] = new Warehouse[10];
            	warehouse = minimize(city,amount,1);
        		refresh_GUI(image_warehouse, warehouse, city, panel, text_total_costs, text_total_distance, 
            			text_amount_warehouse,text_transportation_costs,text_total_demand,image_city);
            	}
        });
        panel.add(button_random);
        
        JButton button_optimize = new JButton("Optimize");
        button_optimize.setBounds(constant_GUI_X_border, constant_GUI_Y_difference*3, button_width, 100);
        button_optimize.setFont(new Font("Arial", Font.PLAIN, 40));
        button_optimize.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) { 
            	Warehouse warehouse[] = new Warehouse[10];
         //   	warehouse = minimize(city,amount,35);
            	warehouse = minimize_all(city);
        		refresh_GUI(image_warehouse, warehouse, city, panel, text_total_costs, text_total_distance, 
            			text_amount_warehouse,text_transportation_costs,text_total_demand,image_city);
            }
        });
        panel.add(button_optimize);
        
        JButton button_start = new JButton("Start Simulation");
        button_start.setBounds(constant_GUI_X_border, 50+constant_GUI_Y_difference*10, button_width, 100);
        button_start.setFont(new Font("Arial", Font.PLAIN, 40));
        button_start.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) { 
            	for (int i=0;i<number_cities;i++) 
            		{city[i].setDemand_ratio(0.01);}
            	current_amount = 1;
            	Warehouse warehouse[] = new Warehouse[10];
            	warehouse = minimize_all(city);
        		refresh_GUI(image_warehouse, warehouse, city, panel, text_total_costs, text_total_distance, 
            			text_amount_warehouse,text_transportation_costs,text_total_demand,image_city);
            }
        });
        panel.add(button_start);
        
        JButton button_progress = new JButton("Progress with next Period");
        button_progress.setBounds(constant_GUI_X_border, 50+constant_GUI_Y_difference*11, button_width, 100);
        button_progress.setFont(new Font("Arial", Font.PLAIN, 40));
        button_progress.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) { 
            	for (int i=0;i<number_cities;i++) 
            		{city[i].progress_in_period(mean, SD);}
            	Warehouse warehouse[] = new Warehouse[10];
            	warehouse = minimize_all(city);
        		refresh_GUI(image_warehouse, warehouse, city, panel, text_total_costs, text_total_distance, 
            			text_amount_warehouse,text_transportation_costs,text_total_demand,image_city);
            }
        });
        panel.add(button_progress);
        
        JButton button_fast_progress = new JButton("Progress with five Periods");
        button_fast_progress.setBounds(constant_GUI_X_2ndborder, 50+constant_GUI_Y_difference*11, button_width, 100);
        button_fast_progress.setFont(new Font("Arial", Font.PLAIN, 40));
        button_fast_progress.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) { 
            	for (int n=0;n<5;n++) {
            		for (int i=0;i<number_cities;i++) 
            			{city[i].progress_in_period(mean, SD);}
            	}
            	Warehouse warehouse[] = new Warehouse[10];
            	warehouse = minimize_all(city);
        		refresh_GUI(image_warehouse, warehouse, city, panel, text_total_costs, text_total_distance, 
            			text_amount_warehouse,text_transportation_costs,text_total_demand,image_city);
            }
        });
        panel.add(button_fast_progress);
        
        JLabel image_germany = new JLabel(new ImageIcon("germany.png"));
        image_germany.setBounds((-55+constant_GUI_X), (-300+constant_GUI_Y),1320, 1844);
        panel.add(image_germany);
        
        // And JPanel needs to be added to the JFrame itself!
        frame.getContentPane().add(panel);
        panel.revalidate();
    }
    
    // this function refreshes the displays
    public static void refresh_GUI(JLabel[] image_warehouse, Warehouse[] warehouse, City[] city, JPanel panel, JLabel text_total_costs, JLabel text_total_distance, JLabel text_amount_warehouse
    		,JLabel text_transportation_costs, JLabel text_total_demand, JLabel[] image_city) {
    	DecimalFormat formatter = new DecimalFormat("#,###.00");
    	DecimalFormat formatter_natural_no = new DecimalFormat("#,###");
    	for (int i=0;i<10;i++) 
    		image_warehouse[i].setVisible(false);
    	for (int i=0;i<amount;i++) {
    		image_warehouse[i].setBounds((warehouse[i].getX()+constant_GUI_X), (1500-warehouse[i].getY()+constant_GUI_Y), 100, 100);
    		image_warehouse[i].setVisible(true);
    	}
        for (int i=0;i<number_cities;i++) {
        	if(city[i].getDemand()>130000)
        		image_city[i].setIcon(new ImageIcon("city_red.png"));
        	else if(city[i].getDemand()>50000)
        		image_city[i].setIcon(new ImageIcon("city_dark_blue.png"));
        	else if(city[i].getDemand()>20000)
        		image_city[i].setIcon(new ImageIcon("city_light_blue.png"));
        	else if(city[i].getDemand()>15000)
        		image_city[i].setIcon(new ImageIcon("city_gray.png"));
        	else
        		image_city[i].setIcon(new ImageIcon("city_white.png"));
        }  
    	text_total_costs.setText("Total Costs/Period: "+formatter.format(total_transportation_costs(city,warehouse,amount)+amount*overhead_costs));
    	text_total_distance.setText("Total Distance: "+formatter_natural_no.format(total_distance(city,warehouse,amount))+" km");
    	text_transportation_costs.setText("Transportation Costs: "+formatter.format(total_transportation_costs(city,warehouse,amount)));
    	text_amount_warehouse.setText("No. Warehouses: "+amount);
    	text_total_demand.setText("Total Demand: "+formatter_natural_no.format(total_demand(city)));
		panel.revalidate();
    }
    
    public static int GetScreenWorkingWidth() {
        return java.awt.GraphicsEnvironment.getLocalGraphicsEnvironment().getMaximumWindowBounds().width;
    }

    public static int GetScreenWorkingHeight() {
        return java.awt.GraphicsEnvironment.getLocalGraphicsEnvironment().getMaximumWindowBounds().height;
    }
}
  
