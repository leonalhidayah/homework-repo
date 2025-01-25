
# homework-repo
# run python 3.12.8
docker run -it python:3.12.8

docker run -it --entrypoint=bash python:3.12.8

# run multiple command with docker build
docker build -t pip:version .
docker run -it pip:version

# download data
wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-10.csv.gz -O green_taxi.csv.gz
wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/misc/taxi_zone_lookup.csv -O zones.csv


# sql
``` sql
-- no 3
-- 3.1
SELECT 
    COUNT(*) AS trip_count
FROM 
    green_taxi_data
WHERE 
    lpep_dropoff_datetime >= '2019-10-01' 
    AND lpep_dropoff_datetime < '2019-11-01'
    AND trip_distance <= 1;

-- 3.2
SELECT 
    COUNT(*) AS trip_count
FROM 
    green_taxi_data
WHERE 
    lpep_dropoff_datetime >= '2019-10-01' 
    AND lpep_dropoff_datetime < '2019-11-01'
    AND trip_distance > 1
	AND trip_distance <= 3;

-- 3.3
SELECT 
    COUNT(*) AS trip_count
FROM 
    green_taxi_data
WHERE 
    lpep_dropoff_datetime >= '2019-10-01' 
    AND lpep_dropoff_datetime < '2019-11-01'
    AND trip_distance > 3
	AND trip_distance <= 7;

-- 3.4
SELECT 
    COUNT(*) AS trip_count
FROM 
    green_taxi_data
WHERE 
    lpep_dropoff_datetime >= '2019-10-01' 
    AND lpep_dropoff_datetime < '2019-11-01'
    AND trip_distance > 7
	AND trip_distance <= 10;

-- 3.5
SELECT 
    COUNT(*) AS trip_count
FROM 
    green_taxi_data
WHERE 
    lpep_dropoff_datetime >= '2019-10-01' 
    AND lpep_dropoff_datetime < '2019-11-01'
    AND trip_distance > 10;


-- no 4
SELECT 
    lpep_pickup_datetime, 
    trip_distance
FROM 
    green_taxi_data
WHERE 
    trip_distance = (
		SELECT 
			MAX(trip_distance) 
		FROM 
			green_taxi_data
	);

-- no 5
SELECT 
	zpu."Zone" AS pickup_location,
	SUM(g.total_amount) AS total_amount
FROM 
	green_taxi_data g 
	INNER JOIN zones zpu 
	ON zpu."LocationID" = g."PULocationID"
	INNER JOIN zones zdo
	ON zdo."LocationID" = g."DOLocationID"
WHERE
	DATE(g.lpep_pickup_datetime) = '2019-10-18'
GROUP BY
	zpu."Zone"
HAVING
	SUM(g.total_amount) > 13000
ORDER BY
	total_amount DESC;


-- no 6
SELECT 
    zdo."Zone" AS dropoff_location,
    MAX(g.tip_amount) AS largest_tip
FROM 
    green_taxi_data g
INNER JOIN zones zpu 
    ON zpu."LocationID" = g."PULocationID"
INNER JOIN zones zdo
    ON zdo."LocationID" = g."DOLocationID"
WHERE 
    g.lpep_pickup_datetime >= '2019-10-01'
    AND g.lpep_pickup_datetime < '2019-11-01'
    AND zpu."Zone" = 'East Harlem North'
GROUP BY 
    zdo."Zone"
ORDER BY 
    largest_tip DESC
LIMIT 1;
```

# no 7
## terraform download
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform

terraform init

terraform apply -auto-approve

terraform destroy -auto-approve
