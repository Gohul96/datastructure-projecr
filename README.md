# datastructure-projecr
Hashing based spell checker

import java.util.*;

public class HashingSpellChecker {
    private HashMap<Integer, List<String>> dictionary;

    public HashingSpellChecker() {
        dictionary = new HashMap<>();
    }

    // Load the dictionary into the hash table
    public void loadDictionary(List<String> words) {
        for (String word : words) {
            int hash = hashFunction(word);
            if (!dictionary.containsKey(hash)) {
                dictionary.put(hash, new ArrayList<>());
            }
            dictionary.get(hash).add(word);
        }
    }

    // Spell checking
    public List<String> checkSpelling(String word) {
        int hash = hashFunction(word);
        if (dictionary.containsKey(hash)) {
            return dictionary.get(hash);
        } else {
            return suggestCorrections(word);
        }
    }

    // Hash function
    private int hashFunction(String word) {
        // A more advanced hash function can be implemented here.
        int hash = 0;
        for (int i = 0; i < word.length(); i++) {
            hash = (hash * 31 + word.charAt(i)) % 10007; // A prime number for better distribution
        }
        return hash;
    }

    // Suggestions for corrections (using Levenshtein distance)
    private List<String> suggestCorrections(String word) {
        List<String> suggestions = new ArrayList<>();
        int threshold = 2; // Maximum edit distance for suggestions
        for (Map.Entry<Integer, List<String>> entry : dictionary.entrySet()) {
            for (String dictWord : entry.getValue()) {
                int distance = levenshteinDistance(word, dictWord);
                if (distance <= threshold) {
                    suggestions.add(dictWord);
                }
            }
        }
        return suggestions;
    }

    private int levenshteinDistance(String word1, String word2) {
        int m = word1.length();
        int n = word2.length();
        int[][] dp = new int[m + 1][n + 1];

        for (int i = 0; i <= m; i++) {
            for (int j = 0; j <= n; j++) {
                if (i == 0) {
                    dp[i][j] = j;
                } else if (j == 0) {
                    dp[i][j] = i;
                } else {
                    int substitutionCost = word1.charAt(i - 1) == word2.charAt(j - 1) ? 0 : 1;
                    dp[i][j] = Math.min(Math.min(dp[i - 1][j] + 1, dp[i][j - 1] + 1), dp[i - 1][j - 1] + substitutionCost);
                }
            }
        }
        return dp[m][n];
    }

    public static void main(String[] args) {
        HashingSpellChecker spellChecker = new HashingSpellChecker();

        // Load a sample dictionary
        List<String> dictionaryWords = new ArrayList<>();
        dictionaryWords.add("apple");
        dictionaryWords.add("banana");
        dictionaryWords.add("cherry");
        // Add more words to the dictionary

        spellChecker.loadDictionary(dictionaryWords);

        // Create a Scanner for user input
        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter a text for spell checking: ");
        String userInput = scanner.nextLine();

        // Tokenize the input into words and check spelling
        String[] words = userInput.split("\\s+");
        for (String word : words) {
            List<String> corrections = spellChecker.checkSpelling(word);

            if (corrections.isEmpty()) {
                System.out.println("No suggestions found for '" + word + "'. The word may be correct.");
            } else {
                System.out.println("Suggestions for '" + word + "':");
                for (String suggestion : corrections) {
                    System.out.println(suggestion);
                }
            }
        }

        // Close the scanner
        scanner.close();
    }
}

