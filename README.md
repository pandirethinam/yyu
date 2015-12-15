public class MainActivity extends Activity {

    private static final String url ="http://52.74.20.2/carhandy/json/getserviceproviderlist.php";
    String responseFromServer;
    ProgressDialog progressDialog;
    Carbean itemBean;
    ListView cinemaList;
    ArrayList<Carbean> resultList = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        new DownloadMovieDetails().execute();

    }

    public class DownloadMovieDetails extends AsyncTask<String, Void, String> {

        @Override
        protected String doInBackground(String... params) {
            try {
                HttpClient client = new DefaultHttpClient();
                HttpGet getRequest = new HttpGet(url);//Forming the request.
                HttpResponse response = client.execute(getRequest);
                HttpEntity responseEntity = response.getEntity();
                responseFromServer = EntityUtils.toString(responseEntity);


            } catch (Exception e) {
                e.printStackTrace();
            }
            return responseFromServer;
        }

        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            progressDialog = new ProgressDialog(MainActivity.this);
            progressDialog.setMessage("Loading Please wait....");
            progressDialog.setCancelable(false);
            progressDialog.show();
        }

        @Override
        protected void onPostExecute(String response) {
            super.onPostExecute(response);
            if (progressDialog.isShowing()) {
                progressDialog.dismiss();
            }
            try {
                JSONObject obj = new JSONObject(response);
                // for (int i=0;i<responseArray.length();i++){
                JSONArray itemaray = obj.getJSONArray("serviceproviderlist");


                for (int i = 0; i < itemaray.length(); i++) {
                    JSONObject item1 = itemaray.getJSONObject(i);
                    itemBean = new Carbean();
                    itemBean.setId(item1.getString("sp_id"));
                    itemBean.setType(item1.getString("sp_type"));
                    itemBean.setPersonlname(item1.getString("service_personnel_name"));
                    itemBean.setImage(item1.getString("sp_image"));
                    itemBean.setAddress1(item1.getString("sp_address1"));
                    itemBean.setAddress2(item1.getString("sp_address2"));
                    itemBean.setSubarea(item1.getString("sp_subarea"));

                    itemBean.setCity(item1.getString("sp_city"));
                    itemBean.setRating(item1.getString("sp_rating"));


                    resultList.add(itemBean);
                }
                cinemaList = (ListView) findViewById(R.id.lv);

                ArealistAdapter adapter = new ArealistAdapter(MainActivity.this, R.layout.arealist, resultList);
                cinemaList.setAdapter(adapter);


            } catch (Exception e) {
                e.printStackTrace();
            }
            Log.e("Response", response);
        }
    }

}
