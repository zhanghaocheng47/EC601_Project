# EC601_Project phase2
I use this script to gain HTML from URL，and return the HTML to the Google NLP API.
I can collect any URL data in this way， and use these data to do futher analysis.
Considering the completeness of the project2, I change my user story and ultimate goal of this project.
I utiize the Google API to analyse the longitude and latitude of the location and this all process could be reversed.

---------------------------------------------------------------------------------------------------------------------------------------------------------
#USER STORY:

I, a student , who is massive for adventure and I usually go to some places which are totally wild and uncivilized and undeveloped.
So that, when I doing adventures in these places, any kinds of map apps have nothing meanning to me. I only want to know is the coordinate
of where I am standing. In this way I can know the deriction I should head for.

I, a worker on privite fishing ship, do not have top equipments like radder and nevigation. All I do on the boat is to catch fish as much as I can. 
However, sometimes I need to send my boat to some remote places. In this case I am easily to get lost on the ocean.

I, a georgrophy student, need to remerber a lot of locations with theri weathers and features and other characteristics. So that I need a search engine to 
help me study that it should return a longitude and latitude of a place when I input a name of a place.

---------------------------------------------------------------------------------------------------------------------------------------------------------------

#MVP:
I could use this function to know the exact loaction of any places on the earth and could also know the name of the place I want to be.
It could be nesserary for some people who is in emergency. When some body get lost and do not know where they should head to and where they 
are standing.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------

#CODING and INSRUCTION

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLConnection;

import cn.liushaoxiong.util.Coordinate;

public class Demo {
	public static void main(String[] args) {
		String addr = GetAddr("35.8616600", "104.1953970");
		System.out.println(addr);
		//getCoordinate("中国");
	}

	/**
	 * Reversely resolve the address based on the latitude and longitude, sometimes it takes a few more attempts
	 * reference from: http://code.google.com/intl/zh-CN/apis/maps/faq.html
	 * Is there a limit on the number of geocoding requests submitted? ) If more than 15,000 address resolution requests from an IP address are received within a 24-hour period, 
   * or from an IP address
   * The address resolution request rate submitted by the address is too fast, and the Google Maps API encoder will start responding with a 620 status code. If the address 
   * resolver is still being used too much, then from this IP Address access to the Google Maps API Geocoder may be permanently blocked.
	 * @param latitude
	 * @param longitude
	 * @return
	 */
	public static String GetAddr(String latitude, String longitude) {
		String addr = "";

		// 
		// The key can be written as a key=abc
		// output=csv.It can also be xml or json, but the data returned by csv is the most concise and easy to parse
		String url = String.format(
				"http://ditu.google.cn/maps/geo?output=csv&key=abcdef&q=%s,%s",
				latitude, longitude);
		URL myURL = null;
		URLConnection httpsConn = null;
		try {
			myURL = new URL(url);
		} catch (MalformedURLException e) {
			e.printStackTrace();
			return null;
		}
		try {
			httpsConn = (URLConnection) myURL.openConnection();
			if (httpsConn != null) {
				InputStreamReader insr = new InputStreamReader(
						httpsConn.getInputStream(), "UTF-8");
				BufferedReader br = new BufferedReader(insr);
				String data = null;
				if ((data = br.readLine()) != null) {
					System.out.println(data);
					String[] retList = data.split(",");
					if (retList.length > 2 && ("200".equals(retList[0]))) {
						addr = retList[2];
						addr = addr.replace("\"", "");
					} else {
						addr = "";
					}
				}
				insr.close();
			}
		} catch (IOException e) {
			e.printStackTrace();
			return null;
		}
		return addr;
	}
	
	public static void getCoordinate(String addr)
	{
		String addrs = "";
	    String address = null;
		try {
			address = java.net.URLEncoder.encode(addr,"UTF-8");
		} catch (UnsupportedEncodingException e1) {
			e1.printStackTrace();
		};
        String output = "csv";
        String key = "abc";
        String url = String.format("http://maps.google.com/maps/geo?q=%s&output=%s&key=%s", address, output, key);
        URL myURL = null;
        URLConnection httpsConn = null;
        //Transcoding
		try {
			myURL = new URL(url);
		} catch (MalformedURLException e) {
			e.printStackTrace();
		}
		
		try {
			httpsConn = (URLConnection) myURL.openConnection();
			if (httpsConn != null) {
				InputStreamReader insr = new InputStreamReader(
						httpsConn.getInputStream(), "UTF-8");
				BufferedReader br = new BufferedReader(insr);
				String data = null;
				if ((data = br.readLine()) != null) {
					System.out.println(data);
					String[] retList = data.split(",");
					/*
		            String latitude = retList[2];
		            String longitude = retList[3];
		            
		            System.out.println("纬度"+ latitude);
		            System.out.println("经度"+ longitude);
		            */

					if (retList.length > 2 && ("200".equals(retList[0]))) {
						addrs = retList[2];
						addrs = addr.replace("\"", "");
					} else {
						addrs = "";
					}
				}
				insr.close();
			}
		} catch (IOException e) {
			e.printStackTrace();
		}    		
		System.out.println(addrs);
	}
}
