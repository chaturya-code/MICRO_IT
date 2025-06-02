# MICRO_IT
#Designing calculator 

import java.util.Scanner;

public class Main{

    // Main method
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("Welcome to Java Calculator");
        System.out.println("Type any expression (e.g. 2+3*4/2) or 'exit' to quit");

        while (true) {
            System.out.print("\nEnter expression: ");
            String input = scanner.nextLine().trim();

            if (input.equalsIgnoreCase("exit")) break;

            try {
                double result = evaluate(input);
                System.out.println("Result = " + result);
            } catch (Exception e) {
                System.out.println("Invalid expression. Error: " + e.getMessage());
            }
        }

        scanner.close();
        System.out.println("Calculator closed.");
    }

    // Expression evaluator
    public static double evaluate(String expr) {
        return new Object() {
            int pos = -1, ch;

            void nextChar() {
                ch = (++pos < expr.length()) ? expr.charAt(pos) : -1;
            }

            boolean eat(int charToEat) {
                while (ch == ' ') nextChar();
                if (ch == charToEat) {
                    nextChar();
                    return true;
                }
                return false;
            }

            double parse() {
                nextChar();
                double x = parseExpression();
                if (pos < expr.length()) throw new RuntimeException("Unexpected character: '" + (char) ch + "'");
                return x;
            }

            // Grammar:
            // expression = term | expression `+` term | expression `-` term
            // term = factor | term `*` factor | term `/` factor
            // factor = `+` factor | `-` factor | number | `(` expression `)`

            double parseExpression() {
                double x = parseTerm();
                while (true) {
                    if      (eat('+')) x += parseTerm(); // addition
                    else if (eat('-')) x -= parseTerm(); // subtraction
                    else return x;
                }
            }

            double parseTerm() {
                double x = parseFactor();
                while (true) {
                    if      (eat('*')) x *= parseFactor(); // multiplication
                    else if (eat('/')) x /= parseFactor(); // division
                    else return x;
                }
            }

            double parseFactor() {
                if (eat('+')) return parseFactor(); // unary plus
                if (eat('-')) return -parseFactor(); // unary minus

                double x;
                int startPos = this.pos;
                if (eat('(')) { // parentheses
                    x = parseExpression();
                    if (!eat(')')) throw new RuntimeException("Missing ')'");
                } else if ((ch >= '0' && ch <= '9') || ch == '.') { // numbers
                    while ((ch >= '0' && ch <= '9') || ch == '.') nextChar();
                    x = Double.parseDouble(expr.substring(startPos, this.pos));
                } else {
                    throw new RuntimeException("Unexpected character: '" + (char) ch + "'");
                }

                return x;
            }
        }.parse();
    }
}

#Designing Courrency Converter

import java.util.HashMap;
import java.util.Scanner;

public class CurrencyConverter {

    // Exchange rates relative to USD
    static HashMap<String, Double> exchangeRates = new HashMap<>();

    static {
        exchangeRates.put("USD", 1.0);     // United States Dollar (USA)
        exchangeRates.put("MYR", 4.5);     // Malaysian Ringgit (Malaysia)
        exchangeRates.put("EUR", 0.85);    // Euro (European Union)
        exchangeRates.put("JPY", 110.0);   // Japanese Yen (Japan)
        exchangeRates.put("GBP", 0.75);    // British Pound Sterling (United Kingdom)
        exchangeRates.put("INR", 82.0);    // Indian Rupee (India)
        exchangeRates.put("CAD", 1.25);    // Canadian Dollar (Canada)
        exchangeRates.put("AUD", 1.4);     // Australian Dollar (Australia)
        exchangeRates.put("CNY", 6.45);    // Chinese Yuan Renminbi (China)
        exchangeRates.put("CHF", 0.92);    // Swiss Franc (Switzerland)
        exchangeRates.put("SGD", 1.35);    // Singapore Dollar (Singapore)
        exchangeRates.put("KRW", 1180.0);  // South Korean Won (South Korea)
        exchangeRates.put("BRL", 5.25);    // Brazilian Real (Brazil)
        exchangeRates.put("RUB", 74.0);    // Russian Ruble (Russia)
        exchangeRates.put("MXN", 20.0);    // Mexican Peso (Mexico)
        exchangeRates.put("ZAR", 14.5);    // South African Rand (South Africa)
        // Add more currencies as needed
    }

    public static double convertCurrency(double amount, String sourceCurrency, String targetCurrency) {
        sourceCurrency = sourceCurrency.toUpperCase();
        targetCurrency = targetCurrency.toUpperCase();

        if (!exchangeRates.containsKey(sourceCurrency)) {
            throw new IllegalArgumentException("Unsupported source currency: " + sourceCurrency);
        }

        if (!exchangeRates.containsKey(targetCurrency)) {
            throw new IllegalArgumentException("Unsupported target currency: " + targetCurrency);
        }

        double usdAmount = amount / exchangeRates.get(sourceCurrency);
        return usdAmount * exchangeRates.get(targetCurrency);
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Convert from (currency code, e.g. USD): ");
        String sourceCurrency = scanner.next();

        System.out.print("Convert to (currency code, e.g. MYR): ");
        String targetCurrency = scanner.next();

        System.out.print("Enter amount in " + sourceCurrency.toUpperCase() + ": ");
        double amount = scanner.nextDouble();

        try {
            double result = convertCurrency(amount, sourceCurrency, targetCurrency);
            System.out.printf("%.2f %s = %.2f %s\n", amount, sourceCurrency.toUpperCase(), result, targetCurrency.toUpperCase());
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
        }

        scanner.close();
    }
}



