# Catalog-Placement-Assignment
import org.json.JSONObject;
import java.io.*;
import java.math.BigInteger;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.*;

public class SecretFinder {

    public static void main(String[] args) throws Exception {
        String[] filenames = {"testcase1.json", "testcase2.json"};

        for (String file : filenames) {
            JSONObject obj = new JSONObject(new String(Files.readAllBytes(Paths.get(file))));
            JSONObject keys = obj.getJSONObject("keys");
            int k = keys.getInt("k");

            List<BigInteger> xList = new ArrayList<>();
            List<BigInteger> yList = new ArrayList<>();

            for (String key : obj.keySet()) {
                if (key.equals("keys")) continue;

                int x = Integer.parseInt(key);
                JSONObject val = obj.getJSONObject(key);
                int base = Integer.parseInt(val.getString("base"));
                BigInteger y = new BigInteger(val.getString("value"), base);

                xList.add(BigInteger.valueOf(x));
                yList.add(y);

                if (xList.size() == k) break;
            }

            BigInteger result = lagrangeInterpolation(xList, yList);
            System.out.println("Secret (c) for " + file + ": " + result);
        }
    }

    public static BigInteger lagrangeInterpolation(List<BigInteger> x, List<BigInteger> y) {
        BigInteger secret = BigInteger.ZERO;
        int k = x.size();

        for (int i = 0; i < k; i++) {
            BigInteger numerator = BigInteger.ONE;
            BigInteger denominator = BigInteger.ONE;

            for (int j = 0; j < k; j++) {
                if (i != j) {
                    numerator = numerator.multiply(x.get(j).negate());
                    denominator = denominator.multiply(x.get(i).subtract(x.get(j)));
                }
            }

            BigInteger term = y.get(i).multiply(numerator).divide(denominator);
            secret = secret.add(term);
        }

        return secret;
    }
}
