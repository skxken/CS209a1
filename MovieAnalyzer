import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.nio.charset.StandardCharsets;
import java.util.*;
import java.util.stream.Collectors;

public class MovieAnalyzer {
    public static class Movie {
        private final String seriesTitle;
        private final int releasedYear;
        private final int runTime;
        private final ArrayList<String> genreList = new ArrayList<>();
        private final double rating;
        private final String[] stars = new String[4];
        private final int gross;
        private final String overview;

        public Movie(String seriesTitle, int releasedYear,
                     int runtime, double rating,
                     int gross, String overview) {
            this.seriesTitle = seriesTitle;
            this.releasedYear = releasedYear;
            this.runTime = runtime;
            this.rating = rating;
            this.gross = gross;
            this.overview = overview;
        }
        
        public void addGenre(String genre) {
            genreList.add(genre);
        }
        
        public void addStar(String[] star) {
            this.stars[0] = star[0];
            this.stars[1] = star[1];
            this.stars[2] = star[2];
            this.stars[3] = star[3];
            Arrays.sort(this.stars);
        }
        
        public boolean hasGenre(String genre) {
            for (String s : genreList) {
                if (s.equals(genre)) {
                    return true;
                }
            }
            return false;
        }
    }
    
    public static ArrayList<Movie> movies = new ArrayList<>();
    
    public MovieAnalyzer(String datasetPath) {
        try (InputStreamReader isr = new InputStreamReader(new FileInputStream(datasetPath), 
                StandardCharsets.UTF_8); BufferedReader infile = new BufferedReader(isr)) {
            String line;
            String[] parts;
            String[] genre;
            String[] stars = new String[4];
            String income;
            String overview;
            int gross;
            line = infile.readLine();
            while ((line = infile.readLine()) != null) {
                parts = line.split(",(?=([^\"]*\"[^\"]*\")*[^\"]*$)", -1);
                genre = parts[5].replace("\"", "").split(", ");
                stars[0] = parts[10]; 
                stars[1] = parts[11]; 
                stars[2] = parts[12]; 
                stars[3] = parts[13];
                income = parts[15].replace(",", "").replace("\"", "");
                overview = parts[7];
                if (overview.charAt(0) == '\"' && overview.charAt(overview.length() - 1) == '\"') {
                    overview = overview.substring(1, overview.length() - 1);
                }
                if (income.length() == 0) {
                    gross = 0;
                } else {
                    gross = Integer.parseInt(income);
                }
                movies.add(new Movie(parts[1].replace("\"", ""), 
                        Integer.parseInt(parts[2].replace("\"", "")), 
                        Integer.parseInt(parts[4].split(" ")[0].replace("\"", "")),
                        Float.parseFloat(parts[6].replace("\"", "")), gross, overview));
                for (String s : genre) {
                    movies.get(movies.size() - 1).addGenre(s);
                }
                movies.get(movies.size() - 1).addStar(stars);
            }
        } catch (IOException e) {
            System.err.println("Fatal error: " + e.getMessage());
        }

    }
    
    public Map<Integer, Integer> getMovieCountByYear() {
        Map<Integer, Integer> m = new HashMap<>();
        for (Movie movie : movies) {
            if (!m.containsKey(movie.releasedYear)) {
                m.put(movie.releasedYear, 1);
            } else {
                int num = m.get(movie.releasedYear);
                m.put(movie.releasedYear, num + 1);
            }
        }
        return m.entrySet()
                .stream()
                .sorted((e1, e2) -> -e1.getKey().compareTo(e2.getKey()))
                .collect(Collectors.toMap(Map.Entry::getKey,
                        Map.Entry::getValue, (e1, e2) -> e1, LinkedHashMap::new));
    }
    
    public Map<String, Integer> getMovieCountByGenre() {
        Map<String, Integer> m = new HashMap<>();
        for (Movie movie : movies) {
            for (int j = 0; j < movie.genreList.size(); j++) {
                if (!m.containsKey(movie.genreList.get(j))) {
                    m.put(movie.genreList.get(j), 1);
                } else {
                    int num = m.get(movie.genreList.get(j));
                    m.put(movie.genreList.get(j), num + 1);
                }
            }
        }
        return m.entrySet()
                .stream()
                .sorted((e1, e2) -> {
                    if (Objects.equals(e1.getValue(), e2.getValue())) {
                        return e1.getKey().compareTo(e2.getKey());
                    } else {
                        return -e1.getValue().compareTo(e2.getValue());
                    }
                })
                .collect(Collectors.toMap(Map.Entry::getKey,
                        Map.Entry::getValue, (e1, e2) -> e1, LinkedHashMap::new));
    }
    
    public Map<List<String>, Integer> getCoStarCount() {
        Map<List<String>, Integer> m = new HashMap<>();
        for (Movie movie : movies) {
            for (int j = 0; j < 4; j++) {
                if (movie.stars[j].equals("")) {
                    continue;
                }
                for (int k = j + 1; k < 4; k++) {
                    if (movie.stars[k].equals("")) {
                        continue;
                    }
                    List<String> list = new ArrayList<>();
                    list.add(movie.stars[j]);
                    list.add(movie.stars[k]);
                    if (!m.containsKey(list)) {
                        m.put(list, 1);
                    } else {
                        int num = m.get(list);
                        m.put(list, num + 1);
                    }
                }
            }
        }
        return m.entrySet()
                .stream()
                .sorted((e1, e2) -> -e1.getValue().compareTo(e2.getValue()))
                .collect(Collectors.toMap(Map.Entry::getKey,
                        Map.Entry::getValue, (e1, e2) -> e1, LinkedHashMap::new));
    }
    
    public List<String> getTopMovies(int topK, String by) {
        List<Movie> list = new ArrayList<>(movies);
        if (by.equals("runtime")) {
            list.sort((o1, o2) -> o1.runTime == o2.runTime
                    ? o1.seriesTitle.compareTo(o2.seriesTitle) : o2.runTime - o1.runTime);
        } else {
            list.sort((o1, o2) -> o1.overview.length() == o2.overview.length()
                    ? o1.seriesTitle.compareTo(o2.seriesTitle)
                    : o2.overview.length() - o1.overview.length());
        }
        List<String> ans = new ArrayList<>();
        for (int i = 0; i < topK; i++) {
            ans.add(list.get(i).seriesTitle);
        }
        return ans;
    }

    public List<String> getTopStars(int topK, String by) {
        Map<String, Double> m1 = new HashMap<>();
        Map<String, Double> m2 = new HashMap<>();
        for (Movie movie : movies) {
            for (int j = 0; j < 4; j++) {
                if (!m2.containsKey(movie.stars[j])) {
                    if (by.equals("rating")) {
                        m2.put(movie.stars[j], 1.0);
                        m1.put(movie.stars[j], movie.rating);
                    } else {
                        if (movie.gross == 0) {
                            continue;
                        }
                        m2.put(movie.stars[j], 1.0);
                        m1.put(movie.stars[j], 1.0 * movie.gross);
                    }
                } else {
                    if (by.equals("rating")) {
                        double num = m2.get(movie.stars[j]);
                        m2.put(movie.stars[j], num + 1);
                        num = m1.get(movie.stars[j]);
                        m1.put(movie.stars[j], num + movie.rating);
                    } else {
                        if (movie.gross == 0) {
                            continue;
                        }
                        double num = m2.get(movie.stars[j]);
                        m2.put(movie.stars[j], num + 1);
                        num = m1.get(movie.stars[j]);
                        m1.put(movie.stars[j], num + 1.0 * movie.gross);
                    }
                }
            }
        }
        Map<String, Double> nodes = new HashMap<>();
        for (Map.Entry<String, Double> entry : m1.entrySet()) {
            String mapKey = entry.getKey();
            Double mapValue = entry.getValue();
            nodes.put(mapKey, mapValue / m2.get(mapKey));
        }
        Map<String, Double> nodes1 = nodes.entrySet()
                .stream()
                .sorted((e1, e2) -> {
                    if (e1.getValue().equals(e2.getValue())) {
                        return e1.getKey().compareTo(e2.getKey());
                    } else {
                        return e2.getValue().compareTo(e1.getValue());
                    }
                })
                .collect(Collectors.toMap(Map.Entry::getKey,
                        Map.Entry::getValue, (e1, e2) -> e1, LinkedHashMap::new));
        ArrayList<String> ans = new ArrayList<>();
        int cnt = 0;
        for (Map.Entry<String, Double> entry : nodes1.entrySet()) {
            cnt++;
            if (cnt > topK) {
                break;
            }
            ans.add(entry.getKey());
        }
        return ans;
    }

    public List<String> searchMovies(String genre, float minRating, int maxRuntime) {
        List<String> lis = new ArrayList<>();
        for (Movie movie : movies) {
            if (movie.hasGenre(genre) && movie.rating >= minRating && movie.runTime <= maxRuntime) {
                lis.add(movie.seriesTitle);
            }
        }
        lis.sort(String::compareTo);
        return lis;
    }
}
