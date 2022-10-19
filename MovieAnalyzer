import java.util.*;
import java.io.*;
import java.util.stream.*;

public class MovieAnalyzer {
    public static class Movie
    {
        private String Series_Title;
        private int Released_Year;
        private String Certificate;
        private int Runtime;
        private ArrayList<String> Genre_list=new ArrayList<>();
        private double IMDB_Rating;
        private int Meta_score;
        private String Director;
        private String[] Stars=new String[4];
        private int No_of_Votes;
        private int Gross;
        private String Overview;
        public Movie(String Series_Title,int Released_Year,String Certificate,int Runtime,double IMDB_Rating,int Meta_score,
                     String Director,int No_of_Votes,int Gross,String Overview)
        {
            this.Series_Title=Series_Title;
            this.Released_Year=Released_Year;
            this.Certificate=Certificate;
            this.Runtime=Runtime;
            this.IMDB_Rating=IMDB_Rating;
            this.Meta_score=Meta_score;
            this.Director=Director;
            this.No_of_Votes=No_of_Votes;
            this.Gross=Gross;
            this.Overview=Overview;
        }
        public void AddGenre(String genre)
        {
            Genre_list.add(genre);
        }
        public void AddStar(String[] Star){
            this.Stars[0]=Star[0];
            this.Stars[1]=Star[1];
            this.Stars[2]=Star[2];
            this.Stars[3]=Star[3];
            Arrays.sort(this.Stars);
        }
        public boolean HasGenre(String genre)
        {
            for(int i=0;i<Genre_list.size();i++)
                if(Genre_list.get(i).equals(genre))
                    return true;
            return false;
        }
    }
    public static ArrayList<Movie> movies=new ArrayList<>();
    public MovieAnalyzer(String dataset_path)
    {
        try (InputStreamReader isr = new InputStreamReader(new FileInputStream(dataset_path), "UTF-8"); BufferedReader infile=new BufferedReader(isr))
        {
            String line;
            String[] parts;
            String[] genre;
            String[] stars=new String[4];
            String income;
            String Overview;
            int meta_score;
            int gross;
            line=infile.readLine();
            while ((line = infile.readLine()) != null)
            {
                parts = line.split(",(?=([^\\\"]*\\\"[^\\\"]*\\\")*[^\\\"]*$)",-1);
                genre=parts[5].replace("\"", "").split(", ");
                stars[0]=parts[10];stars[1]=parts[11];stars[2]=parts[12];stars[3]=parts[13];
                income=parts[15].replace(",","").replace("\"","");
                Overview=parts[7];
                if(Overview.charAt(0)=='\"'&&Overview.charAt(Overview.length()-1)=='\"')
                    Overview=Overview.substring(1,Overview.length()-1);
                if(parts[8].length()==0)
                    meta_score=0;
                else
                    meta_score=Integer.parseInt(parts[8].replace("\"",""));
                if(income.length()==0)
                    gross=0;
                else
                    gross=Integer.parseInt(income);
                movies.add(new Movie(parts[1].replace("\"",""),Integer.parseInt(parts[2].replace("\"","")),parts[3],Integer.parseInt(parts[4].split(" ")[0].replace("\"","")),
                        Float.parseFloat(parts[6].replace("\"","")),meta_score,parts[9],Integer.parseInt(parts[14].replace("\"","")),gross,Overview));
                for(int i=0;i< genre.length;i++)
                    movies.get(movies.size()-1).AddGenre(genre[i]);
                movies.get(movies.size()-1).AddStar(stars);
            }
        }
        catch (IOException e)
        {
            System.err.println("Fatal error: " + e.getMessage());
        }
    }
    public Map<Integer, Integer> getMovieCountByYear()
    {
        Map<Integer,Integer> m=new HashMap<>();
        for(int i=0;i< movies.size();i++)
        {
            if(!m.containsKey(movies.get(i).Released_Year))
                m.put(movies.get(i).Released_Year,1);
            else
            {
                int num=m.get(movies.get(i).Released_Year);
                m.put(movies.get(i).Released_Year,num+1);
            }
        }
        return m.entrySet()
                .stream()
                .sorted((e1, e2) -> {
                        return -e1.getKey().compareTo(e2.getKey());
                })
                .collect(Collectors.toMap(Map.Entry::getKey,
                        Map.Entry::getValue, (e1, e2) -> e1, LinkedHashMap::new));
    }
    public Map<String, Integer> getMovieCountByGenre()
    {
        Map<String,Integer> m=new HashMap<>();
        for(int i=0;i< movies.size();i++)
        {
            for(int j=0;j<movies.get(i).Genre_list.size();j++)
            {
                if(!m.containsKey(movies.get(i).Genre_list.get(j)))
                    m.put(movies.get(i).Genre_list.get(j),1);
                else
                {
                    int num=m.get(movies.get(i).Genre_list.get(j));
                    m.put(movies.get(i).Genre_list.get(j),num+1);
                }
            }
        }
        return m.entrySet()
                .stream()
                .sorted((e1, e2) -> {
                    if (e1.getValue()==e2.getValue()) {
                        return e1.getKey().compareTo(e2.getKey());
                    } else {
                        return -e1.getValue().compareTo(e2.getValue());
                    }
                })
                .collect(Collectors.toMap(Map.Entry::getKey,
                        Map.Entry::getValue, (e1, e2) -> e1, LinkedHashMap::new));
    }
    public Map<List<String>, Integer> getCoStarCount()
    {
        Map<List<String>,Integer> m=new HashMap<>();
        for(int i=0;i< movies.size();i++)
        {
            for(int j=0;j<4;j++)
            {
                if(movies.get(i).Stars[j].equals(""))
                    continue;
                for(int k=j+1;k<4;k++)
                {
                    if(movies.get(i).Stars[k].equals(""))
                        continue;
                    List<String> list=new ArrayList<>();
                    list.add(movies.get(i).Stars[j]);
                    list.add(movies.get(i).Stars[k]);
                    if(!m.containsKey(list)) {
                        m.put(list, 1);
                    }
                    else
                    {
                        int num=m.get(list);
                        m.put(list,num+1);
                    }
                }
            }
        }
        return m.entrySet()
                .stream()
                .sorted((e1, e2) -> {
                        return -e1.getValue().compareTo(e2.getValue());
                })
                .collect(Collectors.toMap(Map.Entry::getKey,
                        Map.Entry::getValue, (e1, e2) -> e1, LinkedHashMap::new));
    }
    public List<String> getTopMovies(int top_k, String by)
    {
        List<Movie> list = new ArrayList<>(movies);
        if(by.equals("runtime"))
            list.sort((o1, o2) -> o1.Runtime == o2.Runtime ? o1.Series_Title.compareTo(o2.Series_Title) : o2.Runtime - o1.Runtime);
        else
            list.sort((o1, o2) -> o1.Overview.length() == o2.Overview.length() ? o1.Series_Title.compareTo(o2.Series_Title) : o2.Overview.length() - o1.Overview.length());
        List<String> ans=new ArrayList<>();
        for(int i=0;i<top_k;i++) {
            ans.add(list.get(i).Series_Title);
        }
        return ans;
    }
    public List<String> getTopStars(int top_k, String by)
    {
        Map<String,Double> m1=new HashMap<>();
        Map<String,Double> m2=new HashMap<>();
        for(int i=0;i<movies.size();i++)
        {
            for(int j=0;j<4;j++) {
                if (!m2.containsKey(movies.get(i).Stars[j])) {
                    if (by.equals("rating")) {
                        m2.put(movies.get(i).Stars[j], 1.0);
                        m1.put(movies.get(i).Stars[j], movies.get(i).IMDB_Rating);
                    } else {
                        if (movies.get(i).Gross == 0)
                            continue;
                        m2.put(movies.get(i).Stars[j], 1.0);
                        m1.put(movies.get(i).Stars[j], 1.0 * movies.get(i).Gross);
                    }
                } else {
                    if (by.equals("rating")) {
                        double num = m2.get(movies.get(i).Stars[j]);
                        m2.put(movies.get(i).Stars[j], num + 1);
                        num = m1.get(movies.get(i).Stars[j]);
                        m1.put(movies.get(i).Stars[j], num + movies.get(i).IMDB_Rating);
                    } else {
                        if (movies.get(i).Gross == 0)
                            continue;
                        double num = m2.get(movies.get(i).Stars[j]);
                        m2.put(movies.get(i).Stars[j], num + 1);
                        num = m1.get(movies.get(i).Stars[j]);
                        m1.put(movies.get(i).Stars[j], num + 1.0 * movies.get(i).Gross);
                    }
                }
            }
        }
        Map<String,Double> nodes=new HashMap<>();
        for (Map.Entry<String, Double> entry : m1.entrySet()) {
            String mapKey = entry.getKey();
            Double mapValue = entry.getValue();
            nodes.put(mapKey,mapValue/m2.get(mapKey));
        }
        Map<String,Double> nodes1=nodes.entrySet()
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
        ArrayList<String> ans=new ArrayList<>();
        int cnt=0;
        for (Map.Entry<String, Double> entry : nodes1.entrySet()) {
            cnt++;
            if(cnt>top_k)
                break;
            ans.add(entry.getKey());
            //System.out.println(entry.getKey()+" "+entry.getValue());
        }
        return ans;
    }
    public List<String> searchMovies(String genre, float min_rating, int max_runtime)
    {
        List<String> lis=new ArrayList<>();
        for(int i=0;i< movies.size();i++)
        {
            if(movies.get(i).HasGenre(genre)&&movies.get(i).IMDB_Rating>=min_rating&&movies.get(i).Runtime<=max_runtime)
                lis.add(movies.get(i).Series_Title);
        }
        lis.sort(String::compareTo);
        return lis;
    }
}
