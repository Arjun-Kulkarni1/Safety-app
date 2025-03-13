import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'package:flutter_local_notifications/flutter_local_notifications.dart';
import 'package:geolocator/geolocator.dart';
//import 'package:flutter_sms/flutter_sms.dart';
import 'package:audioplayers/audioplayers.dart';

final FlutterLocalNotificationsPlugin flutterLocalNotificationsPlugin =
    FlutterLocalNotificationsPlugin();
final AudioPlayer audioPlayer = AudioPlayer();

void initializeNotifications() async {
  const AndroidInitializationSettings initializationSettingsAndroid =
      AndroidInitializationSettings('@mipmap/ic_launcher');

  const InitializationSettings initializationSettings = InitializationSettings(
    android: initializationSettingsAndroid,
  );

  await flutterLocalNotificationsPlugin.initialize(initializationSettings);
}

void showNotification(String title, String body) async {
  const AndroidNotificationDetails androidPlatformChannelSpecifics =
      AndroidNotificationDetails(
    'your_channel_id',
    'your_channel_name',
    importance: Importance.max,
    priority: Priority.high,
  );

  const NotificationDetails platformChannelSpecifics =
      NotificationDetails(android: androidPlatformChannelSpecifics);

  await flutterLocalNotificationsPlugin.show(
    0,
    title,
    body,
    platformChannelSpecifics,
  );
}

Future<Position> getCurrentLocation() async {
  bool serviceEnabled = await Geolocator.isLocationServiceEnabled();
  if (!serviceEnabled) {
    return Future.error('Location services are disabled.');
  }

  LocationPermission permission = await Geolocator.checkPermission();
  if (permission == LocationPermission.denied) {
    permission = await Geolocator.requestPermission();
    if (permission == LocationPermission.denied) {
      return Future.error('Location permissions are denied.');
    }
  }

  if (permission == LocationPermission.deniedForever) {
    return Future.error('Location permissions are permanently denied.');
  }

  return await Geolocator.getCurrentPosition();
}

/*void sendSMS(String message, List<String> recipients) async {
  try {
    await sendSMS(message: message, recipients: recipients);
  } catch (e) {
    print("Error sending SMS: $e");
  }
}*/

void playAudio(String url) async {
  await audioPlayer.play(UrlSource(url));
}

void stopAudio() async {
  await audioPlayer.stop();
}

void main() {
  initializeNotifications();
  runApp(SafetyApp());
}

class SafetyApp extends StatelessWidget {
  const SafetyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: HomeScreen(),
    );
  }
}

class HomeScreen extends StatefulWidget {
  const HomeScreen({super.key});

  @override
  _HomeScreenState createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  bool isSensorActive = false;

  void toggleSensor() async {
    setState(() {
      isSensorActive = !isSensorActive;
    });

    String status = isSensorActive ? "enable" : "disable";
    String url = "http://your-hardware-ip/sensor/$status";

    try {
      await http.get(Uri.parse(url));
    } catch (e) {
      print("Error toggling sensor: $e");
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Two-Wheeler Safety")),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Icon(
              isSensorActive ? Icons.lock : Icons.lock_open,
              size: 100,
              color: isSensorActive ? Colors.green : Colors.red,
            ),
            SizedBox(height: 20),
            Text(
              isSensorActive ? "Sensor Active" : "Sensor Inactive",
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: toggleSensor,
              child: Text(isSensorActive ? "Disable Sensor" : "Enable Sensor"),
            ),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: () async {
                Position position = await getCurrentLocation();
                showNotification(
                  "Location",
                  "Lat: ${position.latitude}, Long: ${position.longitude}",
                );
              },
              child: Text("Get Location"),
            ),
            /*SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                sendSMS("Help! I am in danger!", ["1234567890"]);
              },
              child: Text("Send SOS"),
            )*/
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                playAudio("https://example.com/alert.mp3");
              },
              child: Text("Play Alert"),
            ),
          ],
        ),
      ),
    );
  }
}
