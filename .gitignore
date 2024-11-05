import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.math.BigInteger;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class SecretSharing {

    // Root class to hold the x and y values after decoding
    static class Root {
        int x;
        BigInteger y;

        Root(int x, BigInteger y) {
            this.x = x;
            this.y = y;
        }
    }

    public static void main(String[] args) {
        try {
            // Read JSON input from a file
            String jsonInput = new String(Files.readAllBytes(Paths.get("input.json")));
            List<Map<String, Object>> testCases = parseJSONInput(jsonInput);

            // Process each test case
            int caseNumber = 1;
            for (Map<String, Object> testCase : testCases) {
                System.out.println("Secret for Test Case " + caseNumber + ": " + findSecret(testCase));
                caseNumber++;
            }
        } catch (IOException e) {
            System.out.println("Error reading input file: " + e.getMessage());
        }
    }

    // Method to parse JSON manually into a List of Maps
    private static List<Map<String, Object>> parseJSONInput(String json) {
        List<Map<String, Object>> testCases = new ArrayList<>();
        
        // Simple parsing by extracting test case blocks and parsing the content
        String[] cases = json.split("\\{\\s*\"n\"");

        for (String caseContent : cases) {
            if (!caseContent.trim().isEmpty()) {
                Map<String, Object> testCase = new HashMap<>();
                
                // Extract n, k values
                int n = Integer.parseInt(caseContent.split("\"n\"\\s*:\\s*")[1].split(",")[0].trim());
                int k = Integer.parseInt(caseContent.split("\"k\"\\s*:\\s*")[1].split(",")[0].trim());

                testCase.put("n", n);
                testCase.put("k", k);
                
                // Extract roots as a Map
                Map<Integer, Map<String, String>> roots = new HashMap<>();
                String[] rootEntries = caseContent.split("\\\"(\\d+)\\\"");
                for (int i = 1; i < rootEntries.length; i += 2) {
                    int index = Integer.parseInt(rootEntries[i].trim());
                    String rootData = rootEntries[i + 1];

                    // Parse base and value
                    int base = Integer.parseInt(rootData.split("\"base\"\\s*:\\s*")[1].split(",")[0].trim());
                    String value = rootData.split("\"value\"\\s*:\\s*\"")[1].split("\"")[0];

                    Map<String, String> root = new HashMap<>();
                    root.put("base", String.valueOf(base));
                    root.put("value", value);

                    roots.put(index, root);
                }
                testCase.put("roots", roots);
                testCases.add(testCase);
            }
        }
        return testCases;
    }

    // Method to find the secret constant term (c) for each test case
    private static BigInteger findSecret(Map<String, Object> testCase) {
        int n = (int) testCase.get("n");
        int k = (int) testCase.get("k");
        int m = k - 1;  // Degree of the polynomial

        // Parse roots and decode the y values based on their base
        List<Root> roots = new ArrayList<>();
        Map<Integer, Map<String, String>> rootData = (Map<Integer, Map<String, String>>) testCase.get("roots");

        for (int i = 1; i <= n; i++) {
            Map<String, String> root = rootData.get(i);
            int x = i;
            int base = Integer.parseInt(root.get("base"));
            String valueStr = root.get("value");

            // Convert the value in the given base to a BigInteger in base 10
            BigInteger y = new BigInteger(valueStr, base);
            roots.add(new Root(x, y));
        }

        // Use Lagrange Interpolation to calculate the constant term
        return lagrangeInterpolation(roots, m);
    }

    // Lagrange Interpolation to find f(0), which gives us the constant term c
    private static BigInteger lagrangeInterpolation(List<Root> roots, int m) {
        BigInteger result = BigInteger.ZERO;

        for (int i = 0; i <= m; i++) {
            Root root_i = roots.get(i);

            // Calculate the Lagrange basis polynomial L_i(0)
            BigInteger term = root_i.y;  // Initialize with y_i
            for (int j = 0; j <= m; j++) {
                if (i != j) {
                    Root root_j = roots.get(j);
                    
                    BigInteger numerator = BigInteger.valueOf(-root_j.x);
                    BigInteger denominator = BigInteger.valueOf(root_i.x - root_j.x);
                    
                    // Calculate term *= (0 - x_j) / (x_i - x_j)
                    term = term.multiply(numerator).divide(denominator);
                }
            }
            
            // Add the Lagrange term to the result
            result = result.add(term);
        }

        return result;
    }
}
