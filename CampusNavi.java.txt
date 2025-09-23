import java.util.*;

// ------------------- Base Block class -------------------
abstract class Block {
    protected String name;
    protected List<String> floors;

    public Block(String name) {
        this.name = name;
        this.floors = new ArrayList<>();
    }

    public String getName() {
        return name;
    }

    public void addFloor(String floorDesc) {
        floors.add(floorDesc); // Only store description
    }

    public String getDetails() {
        StringBuilder sb = new StringBuilder();
        sb.append(name).append("\n");
        for (int i = 0; i < floors.size(); i++) {
            if (i == 0) {
                sb.append("Ground Floor: ").append(floors.get(i)).append("\n");
            } else if (i == 1) {
                sb.append("1st Floor: ").append(floors.get(i)).append("\n");
            } else if (i == 2) {
                sb.append("2nd Floor: ").append(floors.get(i)).append("\n");
            } else if (i == 3) {
                sb.append("3rd Floor: ").append(floors.get(i)).append("\n");
            } else {
                sb.append(i).append("th Floor: ").append(floors.get(i)).append("\n");
            }
        }
        return sb.toString();
    }

    @Override
    public String toString() {
        return name;
    }
}

// ------------------- Specific Blocks -------------------
class EntranceGate extends Block { public EntranceGate() { super("Entrance Gate"); } }
class AcademicBlock extends Block { public AcademicBlock() { super("Academic Block"); } }
class MainBlock extends Block { public MainBlock() { super("Main Block"); } }
class MechanicalBlock extends Block { public MechanicalBlock() { super("Mechanical Block"); } }
class CivilBlock extends Block { public CivilBlock() { super("Civil Block"); } }

// ------------------- Main Application -------------------
public class CampusNavigator {
    private Map<Integer, Block> blocksById;
    private Scanner scanner;
    private List<String> pathHistory;  // store visited paths

    public CampusNavigator() {
        blocksById = new LinkedHashMap<>();
        scanner = new Scanner(System.in);
        pathHistory = new ArrayList<>();
        seedCampus();
    }

    // Seed all blocks and details
    private void seedCampus() {
        int id = 1;

        blocksById.put(id++, new EntranceGate());

        // ===== Academic Block with detailed floors and rooms =====
        AcademicBlock academic = new AcademicBlock();
        academic.addFloor("Boys Hostel (Rooms: Room 101, Room 102, Room 103)");       // Ground Floor
        academic.addFloor("Boys Hostel (Rooms: Room 201, Room 202)");                 // 1st Floor
        academic.addFloor("Boys Hostel (Rooms: Room 301, Room 302)");                 // 2nd Floor
        academic.addFloor("Boys Hostel (Rooms: Room 401, Room 402, Room 403)");       // 3rd Floor
        academic.addFloor("Classrooms, Boys Restroom, Girls Restroom, First Year Coordinator Rooms (Rooms: 4401,4402,4403,4404,4405,4406,4407,4408,4409,4410)"); // 4th Floor
        academic.addFloor("Classrooms, Boys Restroom, Girls Restroom, Teachers Staffroom (Rooms: 4501,4502,4503,4504,4506,4507,4508,4509)"); // 5th Floor
        academic.addFloor("Classrooms, Boys Restroom, Girls Restroom, Teachers Staffroom (Rooms: 4601,4602,4603,4604,4606,4607,4608,4609)"); // 6th Floor
        blocksById.put(id++, academic);

        // ===== Main Block with updated floors and rooms =====
        MainBlock main = new MainBlock();
        main.addFloor("BEEE Practical demo lab and Boys Restroom"); // Ground Floor
        main.addFloor("Centre of Examination, Girls Toilet, Rooms: 1101,1102,1103,1104, Robotics and Automation Lab"); // 1st Floor
        main.addFloor("IT HOD Room, IT Dept Staffroom 1, IT Dept Staffroom 2, IT Research Lab, Rooms: 1201,1202,1203,1204, Girls Toilet"); // 2nd Floor
        main.addFloor("CSE HOD Room, CSE Dept Staffroom 1, CSE Dept Staffroom 2, Lab 2, Lab 3, Lab 4, Rooms: 1301,1302,1303,1304,1305,1306,1307,1308, Boys Toilet"); // 3rd Floor
        main.addFloor("AIML Staffroom, Computer Lab, AIML Research Lab, AIML HOD Room, Girls Restroom, Rooms: 1401,1402,1403,1404,1405,1406,1407,1408"); // 4th Floor
        main.addFloor("Boys Restroom, AIDS HOD Room, AIDS Staffroom 1, Rooms: 1501,1502,1503,1504,1505,1506,1507,1508"); // 5th Floor
        blocksById.put(id++, main);

        // Mechanical Block
        MechanicalBlock mechanical = new MechanicalBlock();
        mechanical.addFloor("CAD lab and Staff room");
        mechanical.addFloor("Classrooms and washroom");
        blocksById.put(id++, mechanical);

        // Civil Block
        CivilBlock civil = new CivilBlock();
        civil.addFloor("TRP auditorium");
        civil.addFloor("Library");
        civil.addFloor("Library");
        civil.addFloor("Classroom and Computer lab and washroom");
        civil.addFloor("Seminar Hall");
        blocksById.put(id++, civil);
    }

    // Show all blocks
    private void listBlocks() {
        System.out.println("=== List of Blocks ===");
        for (Map.Entry<Integer, Block> e : blocksById.entrySet()) {
            System.out.println(e.getKey() + ". " + e.getValue().getName());
        }
        System.out.println();
    }

    // Show details of a block
    private void viewBlockDetails() {
        try {
            listBlocks();
            System.out.print("Enter block id: ");
            int choice = Integer.parseInt(scanner.nextLine());
            Block b = blocksById.get(choice);
            if (b == null) {
                System.out.println("Invalid block id.\n");
                return;
            }
            System.out.println("--- Block Details ---");
            System.out.println(b.getDetails());
        } catch (Exception e) {
            System.out.println("Invalid input.\n");
        }
    }

    // Navigation menu with source and destination
    private void showNavigationMenu() {
        try {
            listBlocks();
            System.out.print("Enter source block ID: ");
            int sourceId = Integer.parseInt(scanner.nextLine());
            Block sourceBlock = blocksById.get(sourceId);
            if (sourceBlock == null) {
                System.out.println("Invalid source block ID.\n");
                return;
            }

            System.out.print("Enter destination block ID: ");
            int destId = Integer.parseInt(scanner.nextLine());
            Block destBlock = blocksById.get(destId);
            if (destBlock == null) {
                System.out.println("Invalid destination block ID.\n");
                return;
            }

            navigateFromSourceToDestination(sourceBlock, destBlock);

        } catch (Exception e) {
            System.out.println("Invalid input.\n");
        }
    }

    // Navigation logic based on source and destination
    private void navigateFromSourceToDestination(Block source, Block destination) {
        System.out.println("Navigating from " + source.getName() + " to " + destination.getName() + ":\n");

        // Entrance Gate routes
        if (source instanceof EntranceGate) {
            if (destination instanceof MainBlock) {
                System.out.println("Entrance Gate -> Go straight -> Turn left -> Cross Block 3 (right side) -> Cross Admin Block (right side) -> Continue straight -> Temple (middle) -> Main Block (right side)\n");
                pathHistory.add("Entrance Gate --> Main Block");
            } else if (destination instanceof AcademicBlock) {
                System.out.println("Entrance Gate -> Go straight -> Turn left -> Cross Block 3 (right side) -> Cross Admin Block (right side) -> Continue straight -> Temple (middle) -> Academic Block (left side)\n");
                pathHistory.add("Entrance Gate --> Academic Block");
            } else if (destination instanceof MechanicalBlock) {
                System.out.println("WAY 1: Entrance Gate -> Immediate left -> Vehicle Parking -> Tiny entrance (right side) -> Small pathway -> Mechanical Block (right side)\n");
                System.out.println("WAY 2: Entrance Gate -> Go straight -> Turn left -> Arch (left side) -> Huge open area -> Civil Block (beside TRP Auditorium) -> Mechanical Block (left side)\n");
                pathHistory.add("Entrance Gate --> Mechanical Block");
            } else if (destination instanceof CivilBlock) {
                System.out.println("WAY 1: Entrance Gate -> Immediate left -> Vehicle Parking -> Tiny entrance (right side) -> Small pathway -> Civil Block (left side)\n");
                System.out.println("WAY 2: Entrance Gate -> Go straight -> Turn left -> Arch (left side) -> Huge open area -> Civil Block (beside TRP Auditorium)\n");
                pathHistory.add("Entrance Gate --> Civil Block");
            } else {
                System.out.println("Navigation from Entrance Gate to this block is not defined yet.\n");
            }
        }

        // Academic Block routes
        else if (source instanceof AcademicBlock) {
            if (destination instanceof MainBlock) {
                System.out.println("Academic Block -> Turn left -> Temple on the left -> Cross temple -> Main Block on the left\n");
                pathHistory.add("Academic Block --> Main Block");
            } else if (destination instanceof CivilBlock) {
                System.out.println("Academic Block -> Walk straight -> Girls Hostel on the right -> Cross Girls Hostel -> Huge open area -> Walk straight -> TRP Auditorium on the right -> Walk further -> Civil Block on the right\n");
                pathHistory.add("Academic Block --> Civil Block");
            } else if (destination instanceof MechanicalBlock) {
                System.out.println("Academic Block -> Walk straight -> Girls Hostel on the right -> Cross Girls Hostel -> Huge open area -> Walk straight -> TRP Auditorium on the right -> Walk further -> Civil Block on the right -> Walk further -> Mechanical Block on the right\n");
                pathHistory.add("Academic Block --> Mechanical Block");
            } else if (destination instanceof EntranceGate) {
                System.out.println("Academic Block -> Walk straight -> Mini Canteen on the left -> Cross Admin Block on the left -> Walk further -> Block 3 on the left -> Turn right -> Walk till end of road -> Entrance Gate\n");
                pathHistory.add("Academic Block --> Entrance Gate");
            } else {
                System.out.println("Navigation from Academic Block to this block is not defined yet.\n");
            }
        }

        // Main Block routes
        else if (source instanceof MainBlock) {
            if (destination instanceof AcademicBlock) {
                System.out.println("Main Block -> Turn right -> Temple on the right -> Turn right -> Academic Block\n");
                pathHistory.add("Main Block --> Academic Block");
            } else if (destination instanceof CivilBlock) {
                System.out.println("WAY 1: Main Block -> Turn right -> Temple on the right -> Walk straight -> Girls Hostel -> Turn left -> Huge open area -> Walk straight -> TRP Auditorium on the right -> Walk further -> Civil Block on the right\n");
                System.out.println("WAY 2: Main Block -> Walk straight -> Arch on the right -> Enter through arch -> Huge open area -> Walk straight -> Civil Block\n");
                pathHistory.add("Main Block --> Civil Block");
            } else if (destination instanceof MechanicalBlock) {
                System.out.println("WAY 1: Main Block -> Turn right -> Temple on the right -> Walk straight -> Girls Hostel -> Turn left -> Huge open area -> Walk straight -> TRP Auditorium on the right -> Walk further -> Civil Block on the right -> Walk further -> Mechanical Block on the right\n");
                System.out.println("WAY 2: Main Block -> Walk straight -> Arch on the right -> Enter through arch -> Huge open area -> Walk straight -> Civil Block -> Turn left -> Walk further -> Mechanical Block on the right\n");
                pathHistory.add("Main Block --> Mechanical Block");
            } else if (destination instanceof EntranceGate) {
                System.out.println("Main Block -> Walk straight -> Mini Canteen on the left -> Walk further -> Admin Block on the left -> Walk further -> Block 3 on the left -> Turn right -> Walk till the end of the road -> Entrance Gate\n");
                pathHistory.add("Main Block --> Entrance Gate");
            } else {
                System.out.println("Navigation from Main Block to this block is not defined yet.\n");
            }
        }

        // Civil Block routes
        else if (source instanceof CivilBlock) {
            if (destination instanceof MechanicalBlock) {
                System.out.println("Civil Block -> Turn right -> Walk straight -> Mechanical Block on the right\n");
                pathHistory.add("Civil Block --> Mechanical Block");
            } else if (destination instanceof MainBlock) {
                System.out.println("WAY 1: Civil Block -> Turn left -> Walk straight in the open area -> Girls Hostel on the left -> Turn right -> Walk further -> Temple on the left -> Walk further -> Main Block on the left\n");
                System.out.println("WAY 2: Civil Block -> Walk straight in the open area -> See an arch -> Exit through the arch -> Turn left -> Walk straight -> See a temple -> Turn right -> Main Block\n");
                pathHistory.add("Civil Block --> Main Block");
            } else if (destination instanceof AcademicBlock) {
                System.out.println("WAY 1: Civil Block -> Turn left -> Walk straight in the open area -> Girls Hostel on the left -> Walk further straight -> Academic Block\n");
                System.out.println("WAY 2: Civil Block -> Walk straight in the open area -> See an arch -> Exit through the arch -> Turn left -> Walk straight -> See a temple -> Turn slight left -> Academic Block\n");
                pathHistory.add("Civil Block --> Academic Block");
            } else if (destination instanceof EntranceGate) {
                System.out.println("WAY 1: Civil Block -> Walk straight in the open area -> See an arch -> Exit through the arch -> Turn right -> Walk further straight -> Pass Block 3 on the left -> Turn right -> Walk till the end of the road -> Entrance Gate\n");
                System.out.println("WAY 2: Civil Block -> Turn right -> See a small opening on the right -> Enter -> Pass through a small pathway -> Come out and turn left -> Walk straight in the vehicle parking -> Reach the end -> Turn right -> Entrance Gate\n");
                pathHistory.add("Civil Block --> Entrance Gate");
            } else {
                System.out.println("Navigation from Civil Block to this block is not defined yet.\n");
            }
        }

        // Mechanical Block routes
        else if (source instanceof MechanicalBlock) {
            if (destination instanceof CivilBlock) {
                System.out.println("Mechanical Block -> Turn left -> Walk further straight -> Civil Block on the left\n");
                pathHistory.add("Mechanical Block --> Civil Block");
            } else if (destination instanceof EntranceGate) {
                System.out.println("WAY 1: Mechanical Block -> Turn left -> Walk further straight -> Civil Block on the left -> Turn right -> Walk straight in the open area -> See an arch -> Exit through the arch -> Turn right -> Walk further straight -> Pass Block 3 on the left -> Turn right -> Walk till the end of the road -> Entrance Gate\n");
                System.out.println("WAY 2: Mechanical Block -> Turn left -> See a small opening on the left -> Enter -> Pass through a small pathway -> Come out and turn left -> Walk straight in the vehicle parking -> Reach the end -> Turn right -> Entrance Gate\n");
                pathHistory.add("Mechanical Block --> Entrance Gate");
            } else if (destination instanceof AcademicBlock) {
                System.out.println("WAY 1: Mechanical Block -> Turn left -> Cross Civil Block on the left -> Walk straight in the open area -> Girls Hostel on the left -> Walk further straight -> Academic Block\n");
                System.out.println("WAY 2: Mechanical Block -> Turn left -> Cross Civil Block on the left -> Turn right -> Walk straight in the open area -> See an arch -> Exit through the arch -> Turn left -> Walk straight -> See a temple -> Turn slight left -> Academic Block\n");
                pathHistory.add("Mechanical Block --> Academic Block");
            } else if (destination instanceof MainBlock) {
                System.out.println("WAY 1: Mechanical Block -> Turn left -> Cross Civil Block on the left -> Walk straight in the open area -> Girls Hostel on the left -> Turn right -> Walk further -> Temple on the left -> Walk further -> Main Block on the left\n");
                System.out.println("WAY 2: Mechanical Block -> Turn left -> Cross Civil Block on the left -> Turn right -> Walk straight in the open area -> See an arch -> Exit through the arch -> Turn left -> Walk straight -> See a temple -> Turn right -> Main Block\n");
                pathHistory.add("Mechanical Block --> Main Block");
            } else {
                System.out.println("Navigation from Mechanical Block to this block is not defined yet.\n");
            }
        }

        else {
            System.out.println("Currently, navigation is only defined from Entrance Gate, Academic Block, Main Block, Civil Block, and Mechanical Block.\n");
        }
    }

    // Show path history
    private void showPathHistory() {
        System.out.println("=== Path History ===");
        if (pathHistory.isEmpty()) {
            System.out.println("No navigation history yet.");
        } else {
            for (String p : pathHistory) {
                System.out.println(p);
            }
        }
        System.out.println();
    }

    // Main menu
    public void run() {
        System.out.println("Welcome to Campus Navigation Assistant (with Path History & Source-Destination Navigation)");
        boolean running = true;

        while (running) {
            System.out.println("=== Main Menu ===");
            System.out.println("1. List Blocks");
            System.out.println("2. View Block Details");
            System.out.println("3. Navigate from Source to Destination");
            System.out.println("4. View Path History");
            System.out.println("5. Exit");
            System.out.print("Choice: ");
            String choice = scanner.nextLine();
            System.out.println();

            switch (choice) {
                case "1": listBlocks(); break;
                case "2": viewBlockDetails(); break;
                case "3": showNavigationMenu(); break;
                case "4": showPathHistory(); break;
                case "5": running = false; System.out.println("Goodbye!"); break;
                default: System.out.println("Invalid choice.\n");
            }
        }
    }

    public static void main(String[] args) {
        new CampusNavigator().run();
}
}
