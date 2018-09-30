import java.io.*;
import java.net.*;
import java.text.DateFormat;
import java.util.Date;
import java.util.Locale;
import java.util.Scanner;


public class Main {

    public static void main(String[] args) throws IOException {

        Scanner scanner = new Scanner(System.in);
        System.out.println("Введите ИНН: ");
        String inn = scanner.nextLine();
        String kpp;
        while (true) {
            System.out.println("Введите КПП: ");
            kpp = scanner.nextLine();
            if (kpp.isEmpty()) {
                break;
            }break;}
        String url = "http://npchk.nalog.ru/ajax.html";

        if (!kpp.isEmpty()) {
            url += "?inn=" + inn + "&kpp=" + kpp + "&dt=" + Main.NewTame();
        } else if (kpp.isEmpty()) {
            url += "?inn=" + inn + "&dt=" + Main.NewTame();
        }
        String condition = Main.zapros(url);
        String message = Main.treatmentOfIndividuals(condition);
        System.out.println(message);
    }

    private static String treatmentOfIndividuals(String condition){
        String message;

        switch (condition){
            case "\"0\"": message = "Налогоплательщик зарегистрирован в ЕГРН и имел статус действующего в указанную дату";
                break;
            case "\"1\"": message = "Налогоплательщик зарегистрирован в ЕГРН, но не имел статус действующего в указанную дату";
                break;
            case "\"2\"": message = "Налогоплательщик зарегистрирован в ЕГРН";
                break;
            case "\"3\"": message = "Налогоплательщик с указанным ИНН зарегистрирован в ЕГРН, КПП не соответствует ИНН или не указан*";
                break;
            case "\"4\"": message = "Налогоплательщик с указанным ИНН не зарегистрирован в ЕГРН";
                break;
            case "\"5\"": message = "Некорректный ИНН";
                break;
            case "\"6\"": message = "Недопустимое количество символов ИНН";
                break;
            case "\"7\"": message = "Недопустимое количество символов КПП";
                 break;
            case "\"8\"": message = "Недопустимые символы в ИНН";
                break;
            case "\"9\"": message = "Недопустимые символы в КПП";
                 break;
            case "\"10\"": message = "КПП не должен использоваться при проверке ИП";
                break;
            case "\"11\"": message = "некорректный формат даты";
                break;
            case "\"12\"": message = "некорректная дата (ранее 01.01.1991 или позднее текущей даты)";
                break;
           default: message = "Некорректно введены данные";
               break;
        }
        return message;
    }

    private static String NewTame(){
        Locale local = new Locale("ru","RU");
        DateFormat df = DateFormat.getDateInstance(DateFormat.DEFAULT, local);
        Date currentDate = new Date();
        return df.format(currentDate);
    }

    private static String zapros(String url) throws IOException {
        try {

            URL obj = new URL(url);
            HttpURLConnection connection = (HttpURLConnection) obj.openConnection();
            connection.setRequestMethod("GET");
            BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream()));
            String inputLine;
            StringBuffer response = new StringBuffer();

            while ((inputLine = in.readLine()) != null) {
                response.append(inputLine);
            }

            in.close();
            String a = response.toString();
            return a;

        }catch (IOException e){
            return "";
        }
    }
}

